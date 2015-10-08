# 日志

## 综述
当你使用vine框架创建一个项目的时候，[日志](https://github.com/Andals/vine/tree/master/src/Component/Log)的组件已经涵盖在内了。
因为vine框架高度支持组件化，所以，你也可以使用其他第三方提供的日志组件（例如：`Monolog`）为你的项目提供其他日志方面的支持。

## 日志级别设定

日志级别遵照[psr-3](http://www.php-fig.org/psr/psr-3/)标准中关于日志的级别设定部分。
因此，我们的日志组件也根据[RFC 5424](http://tools.ietf.org/html/rfc5424)中的情况提供8个级别的错误信息，分别是：
emergency, alert, critical, error, warning, notice, info and debug。

```
\Psr\Log\LogLevel::EMERGENCY
\Psr\Log\LogLevel::ALERT
\Psr\Log\LogLevel::CRITICAL
\Psr\Log\LogLevel::ERROR
\Psr\Log\LogLevel::WARNING
\Psr\Log\LogLevel::NOTICE
\Psr\Log\LogLevel::INFO
\Psr\Log\LogLevel::DEBUG
```

## 组件目录结构

```
Log
├── Formater                      //日志内容格式，如某条日志以日期开头等
│   ├── FormaterInterface.php     //日志内容格式接口
│   ├── General.php               //日志格式处理类
│   └── Noop.php                  //日志无需记录时调用的方法
├── Logger.php                    //日志调用类
└── Writer                        //日志写入文件制，如后缀等
    ├── File.php                  //日志文件处理类
    ├── Noop.php                  //日志文件无需写入时调用的方法
    └── WriterInterface.php       //日志写入接口

```

## 使用方法

### 关于生成日志文件
根据传入参数值的不同，日志文件的后缀名称也会不相同，以便用于更好的统计和分析日志。
目前支持三种`日志文件`名字的记录后缀：
- `\Vine\Component\Log\Writer\File::SPLIT_NO`，按照`创建时文件的名字`进行记录；
- `\Vine\Component\Log\Writer\File::SPLIT_BY_DAY`，按照`天`为后缀进行记录；
- `\Vine\Component\Log\Writer\File::SPLIT_BY_HOUR`，按照`小时`为后缀进行记录。

### 关于记录日志级别
如果在项目中设置较多级别的不同日志记录，日志文件只会记录`大于等于`调用时的级别信息，默认级别为 `INFO`。
例如：调用时使用的级别为 `\Psr\Log\LogLevel::ERROR`，那么只会记录 `ERROR`、`CRITICAL`、`ALERT`、`EMERGENCY`级别的日志信息。

## 返回结果

如果不设定任何返回格式，在日志文件中，单条记录将会按照
`[年-月-日 小时-分钟-秒]    访客IP地址    -    日志级别    日志内容`
的格式进行返回，其中分隔符为`\t`。


## 示例

### 代码部分
```
$filePath = '/tmp/test.log';

//定义日志内容
$formater = new \Vine\Component\Log\Formater\General('logTest');
//定义日志输出文件格式
$writer   = new \Vine\Component\Log\Writer\File($filePath, \Vine\Component\Log\Writer\file::SPLIT_NO);

$logger   = new \Vine\Component\Log\Logger($formater, $writer, \Psr\Log\LogLevel::WARNING);

//记录日志
$logger->warning('There may be something wrong with your project.');
```
### 生成结果
如上代码示例会在`/tmp/test.log`中记录如下内容
```
[2015-09-11 05:32:44]    127.0.0.1    logTest    warning    There may be something wrong with your project.

```