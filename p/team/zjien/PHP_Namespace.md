#如何使用PHP命名空间(二)：导入，别名和命名解析
*原文地址*：[http://www.cnblogs.com/TechZi/archive/2011/11/01/2231580.html](http://www.cnblogs.com/TechZi/archive/2011/11/01/2231580.html)
[PHP_Namespace](PHP_Namespace.md)

##首先了解一些PHP术语##
####完全限定名####
任何PHP代码都具有完全限定名——一个以反斜线起始的标识符。例如：\App\Lib1\MYCONST, \App\Lib2\MyFunction()。
完全限定名不会产生任何歧义。首个反斜线与文件系统路径类似，都指明了“根”全局空间。如果在全局空间内实现了一个MyFunction()函数，可在lib1.php和lib2.php中使用“\MyFunction()”对其进行调用。
完全限定名对一次性函数调用或对象初始化有帮助。然而，它们不适用于大量调用的情况。就像接下来要看到的，PHP为命名空间输入提供了其他一些选择。
####部分限定名####
至少有一个命名空间分隔符的标识符。例如：Lib1\MyFunction().
####未限定名####
没有命名空间分隔符的标识符。例如：MyFunction().


----------


在下面的示例中，定义了两段几乎完全相同的代码，唯一的不同之处就在于它们的命名空间。
######*lib1.php*
```php
<?php
// application library 1
namespace App\Lib1;
const MYCONST = 'App\Lib1\MYCONST';
function MyFunction() {
    return __FUNCTION__;
}
class MyClass {
    static function WhoAmI() {
        return __METHOD__;
    }
}
?>
```

######*lib2.php*
```php
<?php
// application library 2
namespace App\Lib2;
const MYCONST = 'App\Lib2\MYCONST';
function MyFunction() {
    return __FUNCTION__;
}
class MyClass {
    static function WhoAmI() {
        return __METHOD__;
    }
}
?>
```


----------


####**在同一命名空间内工作**
######*myapp1.php*
```php
<?php
namespace App\Lib1;
require_once('lib1.php');
require_once('lib2.php');//把两个文件都引入，用于测试
header('Content-type: text/plain');
echo MYCONST . "\n";
echo MyFunction() . "\n";
echo MyClass::WhoAmI() . "\n";
?>
```
虽然同时包含了lib1.php和lib2.php，但MYCONST, MyFunction, and MyClass只会引用lib1.php中的代码。因为myapp1.php的代码同样位于App\lib1命名空间内。

######*output:*
```text
App\Lib1\MYCONST
App\Lib1\MyFunction
App\Lib1\MyClass::WhoAmI
```


----------


####**命名空间导入**
命名空间可通过use操作符导入，例：
######*myapp2.php*
```php
<?php
use App\Lib2;
require_once('lib1.php');
require_once('lib2.php');
header('Content-type: text/plain');
echo Lib2\MYCONST . "\n";
echo Lib2\MyFunction() . "\n";
echo Lib2\MyClass::WhoAmI() . "\n";
?>
```
可使用任意数量的use语句或使用逗号分隔每个独立的命名空间。在此例中，导入了“App\Lib2”命名空间。但此刻仍然无法直接引用MYCONST, MyFunction 或 MyClass，因为调用他们的代码位于全局空间内，PHP将会在全局空间内查找它们。然而，如果我们加入“Lib2\”前缀，它们便形成部分限定名，PHP将会在已导入的命名空间内搜索直到找到匹配项。
######*output:*
```text
App\Lib2\MYCONST
App\Lib2\MyFunction
App\Lib2\MyClass::WhoAmI
```


----------


####**命名空间别名**
命名空间别名或许是最有帮助的功能，用较短的名称来代替较长名称。
myapp3.php
```php
<?php
use App\Lib1 as L;//导入命名空间并且把 "App/Lib1" 别名为 'L'
use App\Lib2\MyClass as Obj;//导入命名空间 并且把"App/Lib2/"命名空间内的类'MyClass' 别名为 'Obj'
header('Content-type: text/plain');//内容类型：纯文本
require_once('lib1.php');
require_once('lib2.php');  //把两个文件都引入，测试下引用谁的代码
echo L\MYCONST . "\n";
echo L\MyFunction() . "\n";
echo L\MyClass::WhoAmI() . "\n";
echo Obj::WhoAmI() . "\n";
?>
```
第一个use语句设定"App/Lib1"的别名为“L”，任何使用"L"的部分限定名都会在编译阶段被解释为“App/Lib1”。因此可使用 L\MYCONST 和 L\MyFunction来代替完全限定名。
第二个use语句非常有趣。它将'Obj'设定为App\Lib2命名空间内的类MyClass的别名。这种别名设定方式只对类有效，常量与函数不适用。
设定类别名后，就可以用new Obj()来创建实例或直接调用静态方法了。

######*output:*
```text
App\Lib1\MYCONST
App\Lib1\MyFunction
App\Lib1\MyClass::WhoAmI
App\Lib2\MyClass::WhoAmI
```


----------


####**PHP命名解析规则**
PHP标识符命名解析遵循以下规则：
1、在编译阶段解析拥有完全限定名的常量、类或函数的调用。

2、未限定和部分限定名根据命名空间导入规则进行解释。例如，如果命名空间A\B\C被导入且设定别名为C，那么C\D\e()调用将被解释为A\B\C\D\e()。

3、在命名空间内，所有未根据命名空间导入规则进行解释的部分限定名都会拥有当前命名空间的前缀。例如：如果在命名空间A\B内执行C\D\e()调用，它将会被解释为 A\B\C\D\e()。

4、未限定的类名会根据当前导入的命名空间进行解释，同时会使用全名来代替缩写后的名称。例如：如果在命名空间A\B内的类C被导入为X，new X()将被解释为 new A\B\C()。

5、命名空间内的未限定函数调用会在运行时解析。例如：如果MyFunction()在命名空间A\B内被调用，PHP首先会搜索函数\A\B\MyFunction()，如果未找到匹配项，PHP会在全局空间内搜索\MyFunction()函数。

6、调用未限定或部分限定名的类会在运行时解析。例如：如果在命名空间A\B内调用new C()，PHP会搜索类A\B\C，如果未能找到匹配项，PHP将会试图自动载入类A\B\C。
