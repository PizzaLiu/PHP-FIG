HTTP消息接口
=====================

本文档描述了在[RFC 7230][]和[RFC 7231][]中被描述来代表HTTP消息通用接口，以及在[RFC 3986][]中规定的URIs语法。

[RFC 7230]:http://tools.ietf.org/html/rfc7230
[RFC 7231]:http://tools.ietf.org/html/rfc7231
[RFC 3986]:http://tools.ietf.org/html/rfc3986
[RFC 2119]: http://www.ietf.org/rfc/rfc2119.txt

HTTP消息是Web开发的基础。Web浏览器和HTTP客户端之间，例如使用cURL创建一个发送到Web服务器的HTTP请求，Web服务器会返回一个HTTP响应。服务器端代码收到一个HTTP请求消息，并返回一个HTTP响应消息。

HTTP消息通常对与最终的消费者用户来说是抽象的，但作为开发者，我们通常需要知道它们是如何构建以及如何访问和操纵它们，以执行我们的任务，是否可能被创建为到HTTP API的请求或处理传入请求。

每一个HTTP请求消息有一个具体形式：
```php
POST /path HTTP/1.1
Host: example.com

foo=bar&baz=bat
```
一个请求的第一行是“请求行”，按照顺序包含了HTTP请求方法，请求的目标地址（通常是一个绝对URI或者服务器上的路径）以及HTTP协议的版本。接着是一个或多个HTTP头，一个空行，以及消息主体。

HTTP响应信息具有类似的结构：
```php
HTTP/1.1 200 OK
Content-Type: text/plain

This is the response body
```
第一行是“状态行”，按照顺序，依次包含了HTTP协议的版本，HTTP状态码，以及一个“原因分析”，也就是一个对人类友好可读的状态码的描述。像请求消息一样，随后一个或多个HTTP头，一个空行，以及消息主体。

本文档中描述的接口都是围绕HTTP消息的抽象和构成它们的元素。

关键词 “必须”("MUST")、“一定不可/一定不能”("MUST NOT")、“需要”("REQUIRED")、
“将会”("SHALL")、“不会”("SHALL NOT")、“应该”("SHOULD")、“不该”("SHOULD NOT")、
“推荐”("RECOMMENDED")、“可以”("MAY")和”可选“("OPTIONAL")的详细描述可参见 [RFC 2119][] 。

### 参考
- [RFC 2119][]
- [RFC 3986][]
- [RFC 7230][]
- [RFC 7231][]


[RFC 2119]:http://tools.ietf.org/html/rfc2119
[RFC 3986]:http://tools.ietf.org/html/rfc3986
[RFC 7230]:http://tools.ietf.org/html/rfc7230
[RFC 7231]:http://tools.ietf.org/html/rfc7231

1. 规范
--------
