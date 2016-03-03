# 流程图

![](https://raw.githubusercontent.com/Andals/vine-doc/master/resource/taskapp.png?v=1.0.0)

# 使用方法

示例来自[vine-demo](https://github.com/Andals/vine-demo)项目

### 执行示例

```
ligang@vbox01 ~ $ /home/ligang/devspace/vine-demo/src/run/task/index.php demo print id=1 name=foo
array(2) {
    ["id"]=>
    int(1)
    ["name"]=>
    string(3) "foo"
}
```

### 执行说明

1. 执行单一入口文件index.php
2. 紧跟的两个参数，第一个为controllerName，第二个为actionName
    1. controllerName和actionName都不，默认controllerName=index，actionName=index
    2. controllerName存在而actionName不存在时，actionName=index
3. 之后的所有key=value的参数，都会是actionParams中的成员
