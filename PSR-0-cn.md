
本文制定了 autoloader 的编写规范。

规范说明
---------
* 一个标准的 命名空间(namespace) 以及 类(class) 的定义必须符合以下结构
`\<Vendor Name>\(<Namespace>\)*<Class Name>`；
* 其中"Vendor Name"为每个命名空间都必须隶属的一个顶级的组织名称；
* 需要的话，每个命名空间下面可以拥有多个子命名空间；
* 当从文件系统载入源文件时，每个命名空间之间的分隔符都要转换成文件夹分隔符 (`DIRECTORY_SEPARATOR`) ；
* `_` 字符在命名空间中是无实际意义的，所以从文件系统载入源文件时，类名称中的每个 `_` 字符也都要转换成文件夹分隔符 (`DIRECTORY_SEPARATOR`) ；
* 从文件系统载入的标准的命名空间以及类文件，都需要以 `.php` 为文件后缀；
* 组织名称、命名空间 以及 类 的名称可以由任意大小写字母组成。

范例
--------
* `\Doctrine\Common\IsolatedClassLoader` => `/path/to/project/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
* `\Symfony\Core\Request` => `/path/to/project/lib/vendor/Symfony/Core/Request.php`
* `\Zend\Acl` => `/path/to/project/lib/vendor/Zend/Acl.php`
* `\Zend\Mail\Message` => `/path/to/project/lib/vendor/Zend/Mail/Message.php`


命名空间以及类名称中的下划线
-----------------------------------------
* `\namespace\package\Class_Name` => `/path/to/project/lib/vendor/namespace/package/Class/Name.php`
* `\namespace\package_name\Class_Name` => `/path/to/project/lib/vendor/namespace/package_name/Class/Name.php`


以上的规范是编写 autoloader 必须遵循的最低规范标准。
你可以通过以下的示例函数 SplClassLoader 载入 PHP 5.3 的类文件，来验证你所写的命名空间以及类是否符合以上规范。


范例实践
----------------------

以下是简单演示 autoloader 规范的例子函数。

```php
<?php

function autoload($className)
{
 $className = ltrim($className, '\\');
 $fileName  = '';
 $namespace = '';
 if ($lastNsPos = strrpos($className, '\\')) {
     $namespace = substr($className, 0, $lastNsPos);
     $className = substr($className, $lastNsPos + 1);
     $fileName  = str_replace('\\', DIRECTORY_SEPARATOR, $namespace) . DIRECTORY_SEPARATOR;
 }
 $fileName .= str_replace('_', DIRECTORY_SEPARATOR, $className) . '.php';

 require $fileName;
}
```

SplClassLoader 实例
-----------------------------
以下的 gist 是 一个 SplClassLoader 类文件的实例，如果你遵循了以上规范，可以把它用来载入你的类文件。
这是目前 PHP 5.3 建议的类文件载入方式。

* [http://gist.github.com/221634](http://gist.github.com/221634)
