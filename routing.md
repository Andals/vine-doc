# 配置路由

```
框架支持自定义路由的实现，具体方式为在BootStrap的Boot方法中添加对应的init方法，如下：
public function boot(\Vine\Component\Container\Web $container)
{/*{{{*/
        $this->initView($container);
        $this->initRouter($container);
}/*}}}*/

添加一个initRouter的方法，下边是示例的实现：

private function initRouter($container)
{/*{{{*/
    $router = new \Vine\Component\Routing\Router();
    $route  = new \Vine\Component\Routing\Route\General();

    $router->addRoute(new Routing\Front\Rule\Live(), $route);
    $router->addRoute(new Routing\Front\Rule\Index(), $route);

    $container->setRouter($router);
}/*}}}*/
我们需要实现的是自己的Rule和Route，Rule代表匹配规则，我们可以自己定义规则，Route代表路由到的路径，Rule的interface定义如下：
interface RuleInterface
{/*{{{*/
    /** 
     * Match rule use request
     * @param \Vine\Component\Http\RequestInterface $request
     * @param \Vine\Component\Routing\Route\RouteInterface $route
     * @return bool
     */
    public function match(\Vine\Component\Http\RequestInterface $request, \Vine\Component\Routing\Route\RouteInterface $route);
}/*}}}*/
我们实现自己的match逻辑，基于request去修改route。
比如我们想实现短连接的路由，将请求/l/xxxxx定向到Pc/feed?feedid=xxxxx示例如下：
class Live implements \Vine\Component\Routing\Rule\RuleInterface
{/*{{{*/
    private $regexList = array(
        '#^l/(?<feedid>\d+)#',
    );  

    public function match(\Vine\Component\Http\RequestInterface $request, \Vine\Component\Routing\Route\RouteInterface $route)
    {/*{{{*/
        foreach ($this->regexList as $regex) {
            if (preg_match($regex, $request->getUrl()->getRelativeUrl(), $matches)) {
                $route->setControllerName('pc');
                $route->setActionName('feed');
                $route->setActionArgs(array('feedId' => $matches['feedid']));

                return true;
            }   
        }   
        return false;
    }/*}}}*/
}

```
