# 概述

在系统启动阶段执行，可以让用户做一些全局自定义的工作，如：

- 注册自己的路由器
- 注册自己的reques及response
- - 注册使用自己的view

等等

# 使用方法

1. 定义一个自己的bootstrap类，继承自\Vine\Bootstrap
2. 实现以init开头的protected方法，参数为\Vine\Loader

# 示例

```
<?php
namespace Vdemo\Bootstrap;

/**
    * This is front bootstrap
 */
class Front extends \Vine\Bootstrap
{/*{{{*/
    public function initView($loader)
    {/*{{{*/
        $view = new \Vine\View\Smarty();
        $view->setViewRoot(\Vdemo\ServerConf::getViewRoot());

        $loader->setView($view);
    }/*}}}*/
}/*}}}*/
```

# boot方法流程图

![](https://raw.githubusercontent.com/Andals/vine-doc/master/images/bootstrap-process.png)
