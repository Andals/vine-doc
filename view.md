# 模板引擎

## 简单PHP模板引擎

框架中的Vine\Component\View\Simple类，实现了一个简单的PHP模板引擎，用法如下：

在Bootstrap中初始化View：

```
private function initView($container)
{
    $view = new \Vine\Component\View\Simple();
    $view->setViewRoot(\Vdemo\ServerConf::getViewRoot());
    $view->setViewSuffix('php');
    $container->setView($view);
}
```
控制器中引入模板变量、渲染页面： 

```
public function indexAction()
{
    $this->assign('name', 'tabalt');
    return $this->autoRender();
}
```
上面的autoRender方法，会默认从模板根目录中寻找当前 module/controller/action 对应的模板文件，然后渲染输出页面。

模板文件中输出变量：

```
hello, <?php echo $name; ?>
```

## 扩展支持第三方模板引擎

vine框架组件化的View支持扩展使用第三方模板引擎，这里以Smarty为例介绍如何来做。

首先，在你项目的`src/app/Lib/View/` 目录下创建一个Smarty.php文件，代码如下：

```
<?php

namespace Vdemo\Lib\View;

class Smarty extends \Vine\Component\View\Base
{

    /**
     * Smarty object
     *
     * @var \Smarty
     */
    protected $smarty;

    /**
     * construct method
     * 
     * @param \Smarty $smarty
     */
    public function __construct(\Smarty $smarty)
    {
        $this->smarty = $smarty;
    }
    
    /**
     * {@inheritdoc}
     */
    public function setViewRoot($viewRoot)
    {
        $this->viewRoot = rtrim($viewRoot, '/') . '/';
        $this->smarty->template_dir = $this->viewRoot;    
    }
    
    /**
     * {@inheritdoc}
     */
    public function assign($key, $value, $secureFilter=true)
    {
        if ($secureFilter) {
            if (is_array($value)) {
                array_walk_recursive($value, function (&$item, $key) {
                    if (is_string($item)) {
                        $item = htmlspecialchars($item);
                    }
                });
            } else if (is_string($value)) {
                $value = htmlspecialchars($value);
            }
        }
        
        $this->smarty->assign($key, $value);
    }
    
    /**
     * {@inheritdoc}
     */
    public function render($viewFile, $withViewSuffix = false, array $data = array())
    {
        $viewFile = $this->getViewFileWithViewRoot($viewFile, $withViewSuffix);
        
        // assin variable
        foreach ($data as $key => $value) {
            $this->assign($key, $value);
        }
        
        ob_start();
        $this->smarty->display($viewFile);
        return ob_get_clean();
    }
}
```

从代码来看，我们实现了Vdemo\Lib\View\Smarty类，继承了 \Vine\Component\View\Base基类，将原生\Smarty的对象在构造方法里注入进来，并利用\Smarty对象的方法，实现了setViewRoot、assign、render三个基本方法。


然后，修改Bootstrap里初始化模板的代码如下：

```
private function initView($container)
{
    $smarty = new \Smarty();
    $smarty->setCompileDir(\Vdemo\ServerConf::getTmpRoot().'/compile');
    $view = new \Vdemo\Lib\View\Smarty($smarty);
    $view->setViewRoot(\Vdemo\ServerConf::getViewRoot());
    $view->setViewSuffix('php');

    $container->setView($view);
}
```
其他用法和自带的PHP引擎是一样了，只是在模板文件中换成使用Smarty的语法。