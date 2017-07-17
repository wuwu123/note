### 垃圾回收

#### HP5.3 之前（有内存泄漏）
```
在PHP5.3版本之前，PHP只有简单的基于引用计数的垃圾回收，当一个变量的引用计数变为0时， <br>
PHP将在内存中销毁这个变量，只是这里的垃圾并不能称之为垃圾。 <br>
并且PHP在一个生命周期结束后就会释放此进程/线程所占的内容，这种方式决定了PHP在前期不需要过多考虑内存的泄露问题。
```
##### 引用计数
每个php变量存在一个叫"zval"的变量容器中。
一个zval变量容器，除了包含变量的类型和值，还包括两个字节的额外信息。
* 第一个是"is_ref"，是个bool值，用来标识这个变量是否是属于引用集合(reference set)。【由于php允许用户
通过使用&来使用自定义引用】，zval变量容器中还有一个内部引用计数机制，来优化内存使用。
* 第二个额外字节是"refcount"，用以表示指向这个zval变量容器的变量(也称符号即symbol)个数。
所有的符号存在一个符号表中，其中每个符号都有作用域(scope)，那些主脚本(比如：通过浏览器请求的的脚本)和每个函数或者方法也都有作用域。

### EXAMPLE
```PHP
<?PHP
$a = "new string";
$b = $a;
xdebug_debug_zval( 'a' );
```
ECHO
```
a: (refcount=2, is_ref=0)='new string'
```
#### HP5.3 之后
```
首先，我们先要建立一些基本规则，如果一个引用计数增加，它将继续被使用，当然就不再在垃圾中。
如果引用计数减少到零，所在变量容器将被清除(free)。
就是说，仅仅在引用计数减少到非零值时，才会产生垃圾周期(garbage cycle)。
其次，在一个垃圾周期中，通过检查引用计数是否减1，并且检查哪些变量容器的引用次数是零，来发现哪部分是垃圾
```