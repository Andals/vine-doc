# 综述

对于psr标准中已规定的部分，请遵照执行，这里不再赘述。
这里主要定义现有[psr](http://www.php-fig.org)标准中未规范的部分。

# 变量

统一使用驼峰式

# 数组key

使用下划线分隔多词

# 判断时常量在等号左边还是右边

统一放在等号右边

```
if ($a === 0){}
```

# 函数中参数如果提供默认值，等号两端是否有空格

有

```
function foo($a = 0){}
```
