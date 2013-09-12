日志接口
================

本文档制定了日志类库的通用接口规范。

本规范的主要目的，是为了让日志类库以简单通用的方式，接收一个 `Psr\Log\LoggerInterface` 对象，来撰写日志文件。

框架以及CMS内容管理系统如有需要， 可以 在本规范的基础上，对此接口进行扩展。

这才能保证在使用第三方的类库文件时，日志接口仍能正常对接。

关键词 “必须”("MUST")、“一定不”("MUST NOT")、“需要”("REQUIRED")、
“将会”("SHALL")、“不会”("SHALL NOT")、“应该”("SHOULD")、“不该”("SHOULD NOT")、
“推荐”("RECOMMENDED")、“可以”("MAY")和”可选“("OPTIONAL")的详细描述可参见[RFC 2119][]。

本文中的 `实现者` 指的是实现了 `LoggerInterface` 接口的类库或者框架，反过来讲，他们就是 `LoggerInterface` 的 `使用者`。

[RFC 2119]: http://tools.ietf.org/html/rfc2119

1. 规范说明
-----------------

### 1.1 基本规范

- `LoggerInterface` 接口对外定义了八个方法，分别用来记录八个 [RFC 5424][] 等级的日志：debug、 info、 notice、 warning、 error、 critical、 alert 以及 emergency 。

- 第九个方法 —— `log`，其第一个参数为记录的等级。使用一个预先定义的等级常量作为参数来调用此方法，必须 与直接调用以上八个方法具有相同的结果。如果参数的常量没有预先定义，则 必须 抛出 `Psr\Log\InvalidArgumentException` 类型的异常。在不确定的情况下，使用者 不该 使用未支持的等级常量来调用此方法。

[RFC 5424]: http://tools.ietf.org/html/rfc5424

### 1.2 记录信息

- 以上每个方法都接受一个字符串类型或者是有 `__toString()` 方法的对象作为记录信息参数，实现者 可以 对此参数进行特别处理，不然，实现者 必须 把它当成字符串来处理。

- 记录信息参数 可以 携带占位符，使得实现者 可以 根据上下文的值将它替换。

其中占位符 必须 与上下文数组中的键名相同。

占位符的名称 必须 由一个左花括号 `{` 以及一个右括号 `}` 包含。但花括号与名称之间 一定不 能有空格符。

占位符的名称 应该 只由 `A-Z`、 `a-z`,
  `0-9`、下划线 `_`、以及英文的句号 `.`组成，其它字符作为将来占位符规范的保留。

实现者 可以 通过对占位符的不同转义或转换，来生成最终的日志。
使用者在不知道上下文的前提下， 不该 提前转义占位符。

以下是一个占位符替换的例子。

```php
/**
 * 在记录信息中的定位符中插入上下文信息。
 */
function interpolate($message, array $context = array())
{
    // 构建一个花括号键名的替换数组。
    $replace = array();
    foreach ($context as $key => $val) {
        $replace['{' . $key . '}'] = $val;
    }

    // 替换记录信息中的占位符，最后返回修改后的记录信息。
    return strtr($message, $replace);
}

// 含有带花括号占位符的记录信息。
$message = "User {username} created";

// 带有替换信息的上下文数组，键名为占位符名称，键值为替换值。
$context = array('username' => 'bolivar');

// 输出 "Username bolivar created"
echo interpolate($message, $context);
```

### 1.3 上下文

- 每个记录函数都接受一个上下文数组参数，用来承载字符串类型无法表示的信息。它 可以 装载任何信息，所以实现者 必须 确保能正确处理其装载的信息，对于其装载的数据，一定不 能抛出异常，或产生PHP出错、警告或提醒信息（error, warning or notice）。

- 如需通过上下文参数传入了一个 `Exception` 对象， 必须 以 `'exception'` 作为键名。
记录异常信息是很普遍的，如果它能够在记录类库的底层实现，就能够让实现者从异常信息中破丝剥茧。
当然，实现者在使用它时，必须 确认 键名为 `'exception'` 的键值是否真的是一个 `Exception`，毕竟它 可以 装载任何信息。

### 1.4 助手类和接口

- `Psr\Log\AbstractLogger` 类使得只需继承它和实现其中的 `log` 方法，就能够很轻易地实现 `LoggerInterface` 接口。
而另外八个方法就能够把记录信息和上下文信息传给她。

-  同样地，使用  `Psr\Log\LoggerTrait`  也只需实现 `log` 方法。
不过，需要注意到的是，在traits函数段还不实现接口前，所以还需要  `implement LoggerInterface`。

- 接口中也提供了  `Psr\Log\NullLogger` ，当记录信息为空时，它使得接口使用者 可以 提供一个备用的“黑洞”(译注：用来记录上下文信息)。
当上下文数据非常重要时，这不失为是一个好方法。

- `Psr\Log\LoggerAwareInterface` 几口仅包括一个
  `setLogger(LoggerInterface $logger)` 方法，框架可以使用它实现自动连接任意的日志记录实例。

  - `Psr\Log\LoggerAwareTrait` trait函数段可以在任何的类里面，只需通过它提供的 `$this->logger`，就可以轻松地实现等同的接口。

  - `Psr\Log\LogLevel` 类负责装载八个记录等级的常量。

  2. 包
  ----------

  上述的接口、类和相关的异常类，以及一系列的实现检测文件，都包含在 [psr/log](https://packagist.org/packages/psr/log) 文件包中.

  3. `Psr\Log\LoggerInterface`
  ----------------------------

  ```php
  <?php

  namespace Psr\Log;

  /**
   * 日志记录实例
   *
   * 日志信息变量 —— message， 必须 是一个字符串或是实现了  __toString() 方法的对象。
   *
   * 日志信息变量中 可以 包含格式如 “{foo}” (代表foo) 的占位符。
   * will be replaced by the context data in key "foo".
   *
   * The context array can contain arbitrary data, the only assumption that
   * can be made by implementors is that if an Exception instance is given
   * to produce a stack trace, it MUST be in a key named "exception".
   *
   * See https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md
   * for the full interface specification.
   */
  interface LoggerInterface
  {
      /**
       * System is unusable.
       *
       * @param string $message
       * @param array $context
       * @return null
       */
      public function emergency($message, array $context = array());

      /**
       * Action must be taken immediately.
       *
       * Example: Entire website down, database unavailable, etc. This should
       * trigger the SMS alerts and wake you up.
       *
       * @param string $message
       * @param array $context
       * @return null
       */
      public function alert($message, array $context = array());

      /**
       * Critical conditions.
       *
       * Example: Application component unavailable, unexpected exception.
       *
       * @param string $message
       * @param array $context
       * @return null
       */
      public function critical($message, array $context = array());

      /**
       * Runtime errors that do not require immediate action but should typically
       * be logged and monitored.
       *
       * @param string $message
       * @param array $context
       * @return null
       */
      public function error($message, array $context = array());

      /**
       * Exceptional occurrences that are not errors.
       *
       * Example: Use of deprecated APIs, poor use of an API, undesirable things
       * that are not necessarily wrong.
       *
       * @param string $message
       * @param array $context
       * @return null
       */
      public function warning($message, array $context = array());

      /**
       * Normal but significant events.
       *
       * @param string $message
       * @param array $context
       * @return null
       */
      public function notice($message, array $context = array());

      /**
       * Interesting events.
       *
       * Example: User logs in, SQL logs.
       *
       * @param string $message
       * @param array $context
       * @return null
       */
      public function info($message, array $context = array());

      /**
       * Detailed debug information.
       *
       * @param string $message
       * @param array $context
       * @return null
       */
      public function debug($message, array $context = array());

      /**
       * Logs with an arbitrary level.
       *
       * @param mixed $level
       * @param string $message
       * @param array $context
       * @return null
       */
      public function log($level, $message, array $context = array());
  }
  ```

  4. `Psr\Log\LoggerAwareInterface`
  ---------------------------------

  ```php
  <?php

  namespace Psr\Log;

  /**
   * Describes a logger-aware instance
   */
  interface LoggerAwareInterface
  {
      /**
       * Sets a logger instance on the object
       *
       * @param LoggerInterface $logger
       * @return null
       */
      public function setLogger(LoggerInterface $logger);
  }
  ```

  5. `Psr\Log\LogLevel`
  ---------------------

  ```php
  <?php

  namespace Psr\Log;

  /**
   * 描述记录等级
   */
  class LogLevel
  {
      const EMERGENCY = 'emergency';
      const ALERT     = 'alert';
      const CRITICAL  = 'critical';
      const ERROR     = 'error';
      const WARNING   = 'warning';
      const NOTICE    = 'notice';
      const INFO      = 'info';
      const DEBUG     = 'debug';
  }
  ```
