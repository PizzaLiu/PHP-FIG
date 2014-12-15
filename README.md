PHP编码规范（中文版）导读
====================================
本文档是PHP互操作性框架制定小组（[PHP-FIG][] :PHP Framework Interoperability Group）制定的PHP编码规范（[PSR][]:Proposing a Standards Recommendation）中译版。

翻译过程中参照了 [莫希爾(Mosil)手札][] 的繁体中文版，以及 [Corrie Zhao][] 组织翻译的简体中文版，
译文中为了让语句通顺，便于理解，没有对原文逐字翻译，个别语句与原文原意可能略有偏差，希望告知指正。

目前官方已制定的规范包括以下六份文件：

  - [PSR-0](https://github.com/PizzaLiu/PHP-FIG/blob/master/PSR-0-cn.md) (已弃用)
  - [PSR-1](https://github.com/PizzaLiu/PHP-FIG/blob/master/PSR-1-basic-coding-standard-cn.md)
  - [PSR-2](https://github.com/PizzaLiu/PHP-FIG/blob/master/PSR-2-coding-style-guide-cn.md)
  - [PSR-2补充](https://github.com/PizzaLiu/PHP-FIG/blob/master/PSR-2-coding-style-guide-meta-cn.md)
  - [PSR-3](https://github.com/PizzaLiu/PHP-FIG/blob/master/PSR-3-logger-interface-cn.md)
  - [PSR-4](https://github.com/PizzaLiu/PHP-FIG/blob/master/PSR-4-autoloader-cn.md)



- 2014/04/25    添加`PSR-2补充`文件以及修改之前版本中的翻译不当与错误。
- 2014/07/31    添加`PSR-4`。

[PHP-FIG]: https://github.com/php-fig/
[PSR]: https://github.com/php-fig/fig-standards
[莫希爾(Mosil)手札]: https://github.com/mosil/fig-standards
[Corrie Zhao]: https://github.com/hfcorriez/fig-standards


以下是原版的导读：

---------------

PHP互操作性框架制定小组
====================================

组建本小组的目的是，通过在各项目的代表之间讨论他们共同的编码规范，以制定一个协作标准。本规范的主要面向对象是本小组的各个组成成员，当然，同时也欢迎关注本规范的其它PHP社区采用本规范。


提交规范建议
------------------------------------

可以通过以下方式给本规范提交建议:

- fork [PSR代码库][]，创建并检出一个分支，在 `proposed/` 下添加 规范建议，然后 push 分支到 Github，最后给我们发送一个 pull request；又或者

- 在 Github 下新建一个讨论 ticket；又或者

- 在 [邮件列表][] 中提交建议。

[邮件列表]: http://groups.google.com/group/php-fig/
[PSR代码库]: https://github.com/php-fig/fig-standards

成为投票成员
---------------------

注意，你 **不需要** 成为投票成员才能在 [邮件列表][] 中发表言论。

想要成为投票成员，你必须发送一封邮件到 [邮件列表][] 中。

- 邮件主题格式如下: `Membership Request: {你的名字} ({参与的项目名称})`

- 邮件内容应包括你的名字、你参与的项目名称、项目的地址以及其它相关信息。
  
目前的成员会对你的加入请求进行投票。

请不要在一份申请中提交多个加入请求，每份申请只能提交一份请求。


目前的成员及其代表项目列表
--------------

1. Nate Abele: Lithium

1. Nils Adermann: phpBB

1. Brett Bieber: PEAR, PEAR2
    
1. Guilherme Blanco: Doctrine, Doctrine2, et al.

1. Jordi Boggiano: Composer, Packagist

1. Pádraic Brady: Zend Framework

1. Karma Dordrak: Zikula

1. Paul Dragoonis: PPI, PPI2

1. William Durand: Propel, Propel 2

1. Don Gilbert: Joomla

1. Cal Evans: the community at large

1. Larry Garfield: Drupal

1. Ivan Habunek: Apache log4php

1. Paul M. Jones: Solar Framework, Aura Project

1. Karsten Dambekalns: TYPO3 Flow, TYPO3 Neos

1. Larry Masters: CakePHP, CakePHP 2

1. John Mertic: SugarCRM

1. Taylor Otwell: Laravel

1. Ryan Parman: Amazon Web Services SDK

1. Evert Pot: SabreDAV

1. Fabien Potencier: Symfony, Symfony2

1. Mike van Riel: phpDocumentor

1. Andre Romcke: eZ Publish

1. Phil Sturgeon: PyroCMS

1. Lukas Smith: Jackalope

1. Kris Wallsmith: Assetic, Buzz

1. David Zülke: Agavi