# 配置路由

```
private function initRouter($container)
{/*{{{*/
    $router = $container->getRouter();

    $route = new \Vine\Component\Routing\Route\General();
    $route->setControllerName('index')->setActionName('index');
    $router->addRoute(new \Vine\Component\Routing\Rule\Prefix('/front/index'), $route);

    $route = new \Vine\Component\Routing\Route\General();
    $route->setControllerName('index')->setActionName('api');
    $router->addRoute(new \Vine\Component\Routing\Rule\Regex('#^/[a-z]+/([0-9]+)$#'), $route);
}/*}}}*/
```
