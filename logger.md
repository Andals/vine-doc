# 日志

## 综述
当你使用vine框架创建一个项目的时候，[日志](https://github.com/Andals/vine/tree/master/src/Component/Log)的组件已经能涵盖在内了。
因为vine框架高度支持组件化，所以，你也可以使用其他第三方提供的日志组件（例如：`Monolog`）为你的项目提供其他日志方面的支持。

## 日志级别设定
-------------------------

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

### 关于日志文件生成
根据传入值的不同,分割类型也会不相同，目前支持三种`日志文件`名字的记录后缀：
0. 按照`创建时文件的名字`进行记录；
1. 按照`天`为后缀进行记录；
2. 按照`小时`为后缀进行记录。

## 完整示例
```
$filePath = '/tmp/test.log';

$formater = new \Vine\Component\Log\Formater\General('logTest');
$writer   = new \Vine\Component\Log\Writer\File($filePath, \Vine\Component\Log\Writer\file::SPLIT_NO);
$logger   = new \Vine\Component\Log\Logger($formater, $writer, \Psr\Log\LogLevel::WARNING);

$logger->warning('There is a warning');
```