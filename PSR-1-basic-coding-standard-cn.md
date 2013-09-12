基本编码规范
=====================
本篇规范制定了编码基本元素的标准，确保共享PHP代码具有较高程度的技术共性。

关键词 “必须”("MUST")、“一定不”("MUST NOT")、“需要”("REQUIRED")、
“将会”("SHALL")、“不会”("SHALL NOT")、“应该”("SHOULD")、“不该”("SHOULD NOT")、
“推荐”("RECOMMENDED")、“可以”("MAY")和”可选“("OPTIONAL")的详细描述可参见[RFC 2119][]。


[RFC 2119]: http://www.ietf.org/rfc/rfc2119.txt
[PSR-0]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md

1. 概览
-----------

- 文件 必须 以 `<?php` 或 `<?=` 标签开始；

- PHP代码文件 必须 以 不带BOM的UTF-8 编码；

- PHP代码中 应该 定义类、函数、常量等声明，或其他会产生 从属效应 的操作，如生成文件输出以及修改.ini配置文件等，并且二者只能选其一；

- 命名空间以及class 必须 符合 [PSR-0][] 规范；

- class名称 必须 遵循 `StudlyCaps` 大写开头的驼峰命名规范；

- class中的常量 必须 所有字符大写，单词由下划线分隔；

- 函数名称 必须 符合 `camelCase` 的小写开头驼峰命名规范。

2. 文件
--------
### 2.1. PHP标签

PHP代码 必须 使用 `<?php ?>` 长标签 或 `<?= ?>` 短输出标签；
一定不 可使用其它自定义标签。

### 2.2. 字符编码
PHP代码 必须 且只可使用不带BOM的UTF-8编码。

### 2.3. 副作用

一份PHP文件 应该 定义新的声明，如类、函数或常量等，
这不会产生副作用，又或者 应该 执行逻辑操作而产生副作用，
但 不可 两者都选择。

”从属效应“（side effects）的意思是，仅仅通过包含文件，不直接声明类、
函数和常量等，而执行的逻辑操作。

”从属效应“ 包含却不仅限于：生成输出、直接的 `require` 或 `include`、连接外部服务、修改 ini 配置、抛出错误或异常、修改全局或静态变量、读或写文件等。

以下反例是一份包含声明以及产生从属效应的代码：

```php
<?php
// 从属效应：修改 ini 配置
ini_set('error_reporting', E_ALL);

// 从属效应：引入文件
include "file.php";

// 从属效应：产生输出
echo "<html>\n";

// 声明函数
function foo()
{
    // 函数主体
}
```
下面是一个范例，一份只包含声明不产生从属效应的代码：

```php
<?php
// 声明
function foo()
{
    // 函数主体
}

// 条件声明 *不* 产生从属效应
if (! function_exists('bar')) {
    function bar()
    {
        // 函数主体
    }
}
```


3. 命名空间以及类命名
----------------------------

命名空间以及类的命名必须遵循 [PSR-0][].

根据规范，每个类都是一份单独的文件，必须隶属于至少一个命名空间：一个顶级的组织名称。

类的命名必须 遵循 `StudlyCaps` 大写开头的驼峰命名规范。

PHP 5.3及以后版本的代码 必须 使用正式的命名空间。

例如：

```php
<?php
// PHP 5.3及以后版本的写法
namespace Vendor\Model;

class Foo
{
}
```
5.2.x及之前的版本 应该 使用伪命名空间的写法，
约定俗成使用 `Vendor_` 为类前缀。

```php
<?php
// 5.2.x及之前版本的写法
class Vendor_Model_Foo
{
}
```

4. 类的常量、属性和方法
-------------------------------------------
此处的”类“指代所有的类、接口以及traits可复用类 
[traits] http://php.net/manual/en/language.oop5.traits.php

### 4.1. 常量
类的常量 必须 所有字母大写，词间以下划线分隔。
参照以下代码：

```php
<?php
namespace Vendor\Model;

class Foo
{
    const VERSION = '1.0';
    const DATE_APPROVED = '2012-06-01';
}
```

### 4.2. 属性

类的属性命名可以遵循 大写开头的驼峰式 如`$StudlyCaps`、小写开头的驼峰式 如`$camelCase` 又或者是 下划线分隔式 如 `$under_score`，
本规范不做强制要求，无论采用遵循哪种命名方式，都 应该 在一定的范围内保持一致。这个范围可以是组织层，包层，类层或方法层。

### 4.3. 方法

函数名称 必须 符合 `camelCase()` 的小写开头驼峰命名规范。