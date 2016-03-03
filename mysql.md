#Mysql使用

Vine框架中的vine/src/Component/Mysql封装了mysql的基本操作，具体结构如下：
```
Mysql
├── Driver.php                 //mysql驱动，封装了初始化、连接mysql、执行、事务等操作
├── Error.php                  //mysql的错误类
├── Dao
│   ├── Base.php               //mysql dao层抽象基类，封装了基本的增删改查操作
│   ├── SimpleSqlBuilder.php   //mysql具体命令的封装
└── Entity                        
    ├── Base.php               //mysql entity层抽象基类
```
该结构的基本思想是业务调用逻辑在svc层，mysql基本操作在dao层，数据表项的过滤等相关操作在entity层，从上到下调用依次是svc -> dao -> entity。

Vine框架中的vine/src/Component/Validator封装了对mysql数据表项的各项检查验证操作，具体结构如下：
```
Validator //封装了对数据表项的各项检查验证操作，比如非空、是Md5值、时间格式合法等
├── Checker.php     
├── Conf.php       
├── ParamException.php
├── Validator.php   
```

##	初始化配置及建立连接：
Db配置如下：
```
$dbConf = array(                       
    'host'        => '127.0.0.1',       
    'user'        => 'root',            
    'pass'        => '123',             
    'name'        => 'test',            
    'port'        => 3306,              
    'persistent'  => false,             
    'charset'     => 'UTF8',            
);   
```
使用时，调用new \Vine\Component\Mysql\Driver($dbConf, $logger);实例化一个mysql驱动，初始化mysql并建立连接。

##	根据业务需要封装mysql的操作
使用时，可以新建一个类继承\Vine\Component\Mysql\Dao\Base，下面举几个封装基本操作的例子：                                                        
（1）查询
```
public function selectByParamsForFront($params = array(), $columnNames = array(), $orderBy = '', $bgn = 0, $cnt = 0) 
{                                                                                                            
	$this->simpleSqlBuilder                                                                                          
    	 ->select($this->getSimpleWhat($columnNames), $this->tableName)                                              
         ->where($this->getSelectFrontColumnComparisons(), $params)                                                  
         ->orderBy($orderBy)                                                                                         
         ->limit($bgn, $cnt); 
       
  return $this->simpleQuery();                                                                                     
}   
 ```
其中，where函数的第一个参数$this->getSelectFrontColumnComparisons()用户在使用时可以自定义，或者传如下这种格式$columnComparisons = array('a' => '=', 'b' => 'in' ...);
第二个参数是字段名-值对组成的数组，如$columnNamesValues = array('a' => 1, 'b' => array(2, 3) ...);

（2）插入
```
public function insert($params = array())                                                                      
{                                                                                                      
	$item = $this->toEntityItemForInsert($params);                                                                                                                                                                    
  	try {                                                                                                      
    	$this->getSqlDao()->insert($item);                                                                     
  	} catch (\PDOException $e) {                                                                               
    	if (\Vine\Component\Mysql\Error::duplicateEntry($e)) {                                                 
      		throw new \Demo\Lib\Error\Exception(ERROR_NO, '插入该项已存在');
    	}                                                                                                      
    	throw $e;                                                                                              
  	}                                                                                                                                                                                                                   
  	return $item;                                                                                              
}                                                                                                    
```
```
protected function toEntityItemForInsert($item)                                             
{                                                                                                                                                                                                           
	  $clsName = '\Demo\Model\Entity\\'.$this->entityName;                                    
	  $entity  = new $clsName();                                                              
	  $entity->setValidator(new \Vine\Component\Validator\Validator());                       
	  $entity->setColumnsValues($item);                                                                                                                                          
	  return $entity->toItem();                                                               
}   
```
```
public function setColumnsValues($item)                                                  
{                                                                           
	  if (!is_null($this->validator) && method_exists($this, 'setColumnsValidatorConf')) { 
	    $this->setColumnsValidatorConf($this->validator->getConf());                     
	     $this->columns = $this->validator->filterParams($item);                          
	  } else {                                                                             
	    foreach ($this->columns as $name => $value) {                                    
	      if (isset($item[$name])) {                                                   
	        $this->columns[$name] = $item[$name];                                    
	      }                                                                            
	    }                                                                                
	  }                                                                                    
}                                                                                                 
```  
其中，'\Demo\Model\Entity\\'.$this->entityName类继承Vine框架中\Vine\Component\Mysql\Entity\Base类； Entity基类中的 setValidator()函数初始化验证类； 
setColumnsValues()函数可以根据用户自定义的各种验证规则对传进来的’字段名-值’对数组进行过滤。
用户如果需要对各数据项进行过滤，可自行实现setColumnsValidatorConf函数，vine-demo中有个具体例子。

（3）更新
``` 
public function updateById($id, $columnNamesValues)
{
	  $this->simpleSqlBuilder                               
	       ->update($this->tableName)                       
	       ->set($columnNamesValues)
	       ->where(array('id' => '='), array('id' => $id)); 
	
	    return $this->simpleExecute();                        
}
``` 

（4）删除
``` 
public function deleteById($id)                           
{
    $this->simpleSqlBuilder                               
         ->delete($this->tableName)                       
         ->where(array('id' => '='), array('id' => $id)); 
                                                          
    return $this->simpleExecute();                        
}
``` 

最后，vine/src/Component/Mysql/Dao/SimpleSqlBuilder.php 文件中where()函数的具体实现buildComparison() 和 buildComparisonIn()可看出vine框架中Mysql的查询、更新、删除、插入操作均采用参数绑定的方式
，并用 ？占位符来代表参数绑定，参数绑定可以避免SQL注入攻击。


