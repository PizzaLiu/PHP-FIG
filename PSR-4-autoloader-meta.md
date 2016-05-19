PSR-4 标准补充
===================

1. 概述
----------

本文意图制定统一的 PHP 加载器的命名空间映射文件系统路径的规则，并且能与其他的 SPL 注册自动加
载器并存。本文只作为补充，并不能替代 PSR-0。

2. 为什么这样做？
--------------

### 以前的 PSR-0

PSR-0 类命名和自动加载标准提出自 PHP 5.2 和之前版本被广泛接受的 Horde/REAR 约定之下，该约
定倾向把所有的 PHP 类源码放在单一的主目录中，在类名中使用下划线指示伪命名空间，像下面这样:

    /path/to/src/
        VendorFoo/
            Bar/
                Baz.php     # VendorFoo_Bar_Baz
        VendorDib/
            Zim/
                Gir.php     # Vendor_Dib_Zim_Gir

随着 PHP 5.3 发布，合适的命名空间语法可以被使用，PSR-0 被引入允许旧式的 Horde/PEAR 下划线
模式 *和* 使用新的命名空间表示法。为了缓解旧式命名到新式命名的过渡，下划线仍然允许在类名中使用
，由此支持更宽使用范围。

    /path/to/src/
        VendorFoo/
            Bar/
                Baz.php     # VendorFoo_Bar_Baz
        VendorDib/
            Zim/
                Gir.php     # VendorDib_Zim_Gir
        Irk_Operation/
            Impending_Doom/
                V1.php
                V2.php      # Irk_Operation\Impending_Doom\V2

这种结构在使用 PEAR 安装器从 PEAR 包移动源文件到单一的中央目录的情况下，是非常明智的。

### Composer 的到来

Composer 包源不在复制到单一的全局位置，使用他们在他们安装的位置并且不来回移动。这意味着，
Composer 没有PEAR 作为 PHP 源的 "单一主目录"。相反，有多个目录，每个包在每个项目独立的目录
中。

为了迎合 PSR-0 的要求，这导致 Composer 的包看起来像这样:

    vendor/
        vendor_name/
            package_name/
                src/
                    Vendor_Name/
                        Package_Name/
                            ClassName.php       # Vendor_Name\Package_Name\ClassName
                tests/
                    Vendor_Name/
                        Package_Name/
                            ClassNameTest.php   # Vendor_Name\Package_Name\ClassNameTest

"src" 和 "tests" 目录必须包含 vendor 和包目录名称，这是遵从 PSR-0 的产物。

很多人发现这种结构比必要的更深和更多重复。本提案建议补充或替代 PSR 将会更有用，如这样做我们的
包看起来像:

    vendor/
        vendor_name/
            package_name/
                src/
                    ClassName.php       # Vendor_Name\Package_Name\ClassName
                tests/
                    ClassNameTest.php   # Vendor_Name\Package_Name\ClassNameTest

这将需要实现最初被称为 "面向-包的自动加载" (作为对比传统的 "直接的类-到-文件的自动加载")。

### 面向-包的自动加载

通过扩展或修订 PSR-0 实现面向-包的自动加载非常困难，因为 PSR-0 不允许修改类名路径之间的任何
部分。这意味着实现面向-包的自动加载要比 PSR-0 复杂的多，但是，它将使得包变的清洁。

最初，以下规则是建议的:

1. 实现者必须使用两个以上的命名空间层级: 一个 vendor 名，和该 vendor 内的包名。(这两个顶级
名称组合被简称为 vendor-package 或 vendor-package namespace。)

2. 实现者必须允许 vendor-package namespace 与完全限定类名的其余部分之间的路径中缀。

3. vendor-package namespace 可以映射到任意目录。完全限定类名的其余部分，必须映射命名空间名
称到同名目录，类名必须映射到 .php 结尾的同名文件。

注意这意味着结束了在类名中下划线作为目录分隔符的做法。有人可能认为下划线应该被遵从因为它们出现
在 PSR-0 规范当中，但是在该文档中它们作为 PHP 5.2 或者更旧的版本的伪命名空间过渡的做法，所以
它们最好在这里被删除。


3. 范围
--------

### 3.1 目标

- 保留实现者必须使用两个以上的命名空间层级的 PSR-0 规则: 一个 vendor 名，和该 vendor 内的
  包名。

- 允许 vendor-package namespace 与完全限定类名的其余部分的路径中缀。

- 允许 vendor-package namespace 可以映射到任何目录，也可能是多个目录。

- 结束遵从类名中下划线作为目录分隔符的做法。

### 3.2 非目标

- 为非类资源提供通用的转换规则


4. 方案
-------------

### 4.1 最佳方案

本方案保留了 PSR-0 关键特性，同时消除了更深层次的目录结构。此外，指定了一些附加规则使得更明确
的互操作性实现。

尽管不涉及目录映射，最终草案还是规定了自动加载器应该如何处理错误。具体来说，它禁止抛出异常或提
出错误，这有两方面的原因。

1. PHP 中自动加载器设计是可堆叠的，如果一个自动加载器不能加载，则其他的仍有机会继续加载。若有
其中一个自动加载器发生错误此过程将不会进行下去。

2. `class_exists()` 和 `interface_exists()` 允许在正常自动加载之后找不到类或接口，若自
动加载器抛出异常将使得 `class_exists()` 不可用，从互操作性的角度来看这是无法接受的。自动加载
器在找不到类的情况下最好通过日志记录提供附加的调试信息，日志可以使用 PSR-3 兼容日志记录或其他
什么。

优点:

- 较浅的目录结构

- 文件位置更加固定

- 不再使用类名中下划线作为目录分隔符

- 更明确的互操作性实现

缺点:

- 不能像 PSR-0 仅仅通过类名就能确定它在文件系统的具体位置 (这种 "类-到-文件" 约定继承自
  Horde/PEAR)。


### 4.2 备选方案: 仍然遵循 PSR-0 标准

仍然遵循 PSR-0 标准，这尽管合理，但只会留下较深的目录结构。

优点:

- 不需要改变任何惯例或实现

缺点:

- 留下更深的目录结构

- 留下遵从类名中使用下划线作为目录分隔符的做法


### 4.3 备选方案: 自动加载与转换分离

Beau Simensen 和其他人建议，转换逻辑可以从自动加载提案中分离，这样其他提案可以引用转换规则。
对它们的分离工作，日后会通过投票和讨论进行，合并版本 (如: 转换规则嵌入自动加载提案) 做为优先采
用项。

优点:

- 其他提案可以引用分离的转换规则

缺点:

- 不符合投票和一些合作者的意愿

### 4.4 备选方案: 使用更多的祈使句和叙述语言

第二轮投票之后，发起人听到最多的投票是他们支持这个想法但不同意提案的措辞 (或解释)，有段时间提案
投票扩大到更多叙述和一些更命令式的语言上，这样的做法被少数直言不讳的参与者谴责。之后的一段时间
Beau Simensen 开始着眼与 PSR-0 的实验性修订，编者和发起人更青睐这种简明的做法并沿用至当前审
议中的版本 (由贡献最多的 Paul M. Jones 编写)。

### PHP 5.3.3 之前版本的兼容

PHP 5.3.3 之前的版本不舍去前缀的命名空间，因此找出具体实例落在了具体的声明上。舍去前缀的命名
空间会导致异常发生。


5. 人员
---------

### 5.1 编者

- Paul M. Jones, Solar/Aura

### 5.2 发起人

- Phil Sturgeon, PyroCMS (Coordinator)
- Larry Garfield, Drupal

### 5.3 贡献者

- Andreas Hennings
- Bernhard Schussek
- Beau Simensen
- Donald Gilbert
- Mike van Riel
- Paul Dragoonis
- 和其他不计其数的贡献者


6. 投票
--------

- **投票入口:** <https://groups.google.com/d/msg/php-fig/_LYBgfcEoFE/ZwFTvVTIl4AJ>

- **采纳的投票:**

    - 第一次: <https://groups.google.com/forum/#!topic/php-fig/Ua46E344_Ls>,
      提出之前的新工作流; 因意外的提案修改中止

    - 第二次: <https://groups.google.com/forum/#!topic/php-fig/NWfyAeF7Psk>,
      取消发起人的决定权 <https://groups.google.com/forum/#!topic/php-fig/t4mW2TQF7iE>

    - 第三次: 待定


7. 相关链接
-----------------

- [Autoloader, round 4](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/lpmJcmkNYjM)
- [POLL: Autoloader: Split or Combined?](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/fGwA6XHlYhI)
- [PSR-X autoloader spec: Loopholes, ambiguities](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/kUbzJAbHxmg)
- [Autoloader: Combine Proposals?](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/422dFBGs1Yc)
- [Package-Oriented Autoloader, Round 2](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/Y4xc71Q3YEQ)
- [Autoloader: looking again at namespace](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/bnoiTxE8L28)
- [DISCUSSION: Package-Oriented Autoloader - vote against](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/SJTL1ec46II)
- [VOTE: Package-Oriented Autoloader](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/Ua46E344_Ls)
- [Proposal: Package-Oriented Autoloader](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/qT7mEy0RIuI)
- [Towards a Package Oriented Autoloader](https://groups.google.com/forum/#!searchin/php-fig/package$20oriented$20autoloader/php-fig/JdR-g8ZxKa8/jJr80ard-ekJ)
- [List of Alternative PSR-4 Proposals](https://groups.google.com/forum/#!topic/php-fig/oXr-2TU1lQY)
- [Summary of [post-Acceptance Vote pull] PSR-4 discussions](https://groups.google.com/forum/#!searchin/php-fig/psr-4$20summary/php-fig/bSTwUX58NhE/YPcFgBjwvpEJ)
