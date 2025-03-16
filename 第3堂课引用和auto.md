引用的使用场景：（只有C++里有引用）

1，做参数，做输出型参数，（指针也可以做到，可代替传址传参）函数内部形参的改变要改变函数外的实参,

比如Swap函数，链表头指针的指向改变时，

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112786.png" alt="image-20240907122649763" style="zoom:67%;" />

C++里把结构体升级成了类，所以能直接写成36行那样

2，做参数，提高效率，（变量也可以做到，可代替传值传参）主要针对大对象 / 深拷贝

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112787.png" alt="image-20240907123224585" style="zoom:50%;" />

由于引用不开空间，在传参的时候就不用花费太多时间了，在对象特别大的时候传值就比较慢，提升效率的效果就很明显。

3，引用做返回值

如果是正常状态下，作返回值的变量，在返回的时候会先生成一个临时变量存放这个返回值

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112788.png" alt="image-20240907124703161" style="zoom: 50%;" />

而如果用引用做返回值，在返回的时候就不会生成临时变量，既节省了空间也节约了时间，减少了拷贝提高了效率。

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112790.png" alt="image-20240907124758661" style="zoom:50%;" />

这个时候返回值类型是int，是小对象，差别不是很大，如果是大对象的话差异就很大了，因为大对象在拷贝的时候会花费较多的时间。

值得注意的是，上面int的例子是静态，出了函数以后空间不会被回收，所以用引用没有问题，因为返回的是一个别名，值可以直接在函数里面对应别名的地方找，如果上面int的例子不是静态而是一个局部变量，那这里就不一定能找到了，因为局部变量出了函数以后空间会被收回，返回出来的别名不一定有用，当然还有侥幸的情况。

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112791.png" alt="image-20240907125602335" style="zoom:67%;" />

如果第一种情况，栈帧销毁但还没有清理，此时访问别名n的这块空间属于是越界访问，已经无法保证值的正确性了，但越界访问不一定会报错。有的编译器会保留下来栈帧销毁前的空间的值，有的只会留下一个随机值，VS会保留下来，

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112792.png" alt="image-20240907130323570" style="zoom:67%;" />

cout和count中间的这个Conut（20），是由于调用这个函数的时候，栈帧覆盖了，正好前后的n空间都是同一块空间，所以ret指向的变成21了。

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112793.png" alt="image-20240907224408067" style="zoom:50%;" />

比如这种情况，中间插了个函数，就导致栈帧就不是原先那个空间了。

所以引用做返回值，有一定的风险，有局部变量空间回收的风险。只要把局部变量变成static int静态变量，就可以保留该局部变量，消除这个风险。 只要出了作用域，返回变量还在，就可以用

引用使用方便的实例：

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112794.png" alt="image-20240907231423784" style="zoom:50%;" />



直接把函数本身当做一个变量来使用了，因为用的是引用，

这就是引用的第四个功能，修改返回值+获取返回值，功能非常强大且简洁

![image-20240907231655020](https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112795.png)

下面展示一下C++的写法

C++把结构体升级成了类， 	 

 <img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112796.png" alt="image-20240907232503441" style="zoom: 50%;" />

接

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112797.png" alt="image-20240907232700544" style="zoom:67%;" />

关于常引用的问题：

在引用过程中，权限不能放大，但是权限可以平移或者缩小。

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112798.png" alt="image-20240907233650961" style="zoom: 50%;" />

正常使用的时候是权限平移，int移到int

如果是第一种，就是const int 转int 权限范围变大了，就不允许了，就会报错。

![image-20240907233843577](https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112799.png)



此处是没有问题的，因为 x 是 int 权限的，可以更改，如果是要z++的话就不行了，会有语法错误。

如果是x++，z作为引用的性质，z仍然会++，但如果是z++，就会直接报错，影响不到x。

关于类型不同之间的引用：

![image-20240907234440630](https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112800.png)

看似是由于类型不同导致的报错，

但把int&  改成const int& 的时候，发现就不报错了。

其实是因为，在类型转换的时候（这个跟返回值临时变量那个不是一回事，那个引用没有临时变量，类型转换的时候引用也会有临时变量），等式右边的变量值会先放到一个临时变量里面，

这个临时变量具有常性，与等式左边的类型对应，比如dd转化到 ii ，double类型的dd值会先放到int临时变量，比如dd转化到rii，会先放到 int 临时变量里面，这个临时变量具有常性，相当于const int ，也就是说目前这个权限是 const int 的权限，而rii是int权限，这属于是权限放大，因此转换不过去会报错，于是如果把int&改成const int&，便可以逢凶化吉。

![image-20240907235033459](https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112801.png)

返回值临时变量的情况是，传值返回（非引用返回）的时候，不论静态static int还是正常int 都会生成临时变量，这个临时变量也是常性的，因此不能转到int

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112802.png" alt="image-20240908114330005" style="zoom: 67%;" />

这种情况下就可以把int& 改成const int& ，就很不会报错了。

这是传值返回的时候，因为涉及到临时变量，所以要注意常性的问题，如果是引用返回，并且是int& func1，那么不用临时变量，fuc1（）仍然是int&类型而不是const int，所以

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112803.png" alt="image-20240908115548186" style="zoom:50%;" />

传值返回。即使是在外面引用，也是传值返回，跟引用返回不是一个档次。

关于权限放大平移问题，只有引用参与的时候才会涉及到这个权限，如果仅仅是const int 转int 拷贝，完全不涉及权限问题。

例子：

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112804.png" alt="image-20240909215706384" style="zoom:50%;" />

在这里 i 会把值放到临时变量里，因为不可能去改变 i 的存储方式。



<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112805.png" alt="image-20240909220753717" style="zoom:50%;" />

在底层汇编指令层面看，引用是类似指针的方式实现的，底层汇编代码一样。、

引用和指针的区别：

![image-20240909220942315](https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112806.png)

其他一些关键字：

auto：自动类型，能根据右边的表达式自动推导c的类型

例如 auto c = a;   c的类型就是a的类型，

auto   d=1+1.1   ；  d的类型就是double

typeid(c).name()  可以显示某个变量的类型

![image-20240909221946731](https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112807.png)

auto相比于普通类型而不适用的场景：

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112808.png" alt="image-20240909222048077" style="zoom:67%;" />

5，auto变量不能做返回值，C++14以后可以做返回值。

对于第4条，可以用auto写新式简洁的for循环

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112809.png" alt="image-20240909222408636" style="zoom:67%;" />

这个意思是从arr中依次取数值赋值给e，自动迭代，自动结束

如果要通过这种方式对数组修改，也是可以的，但是要注意，：

如果还像上面一样，从arr中依次取数值赋值给e的话，无法修改数组中的，因为修改的是e而不是数组，这种情况只要加个引用就可以了，让e成为别名，这样对e修改就是对数组修改了。

需要注意的是，arr必须是个数组而不是一个指针，比如传入的指针，此时array不是个数组

![image-20240909222805783](https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112810.png)

auto*  b= &x;  也可以进行自动推导，但等号右边必须是指针类型，肯定不能放个非指针。

对于NULL的问题，在传统C语言里面，NULL就是0

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112811.png" alt="image-20240910231438071" style="zoom:67%;" />

于是在下面的代码中，都会只调用第一个函数

<img src="https://raw.githubusercontent.com/zlskll/picture/main/test/202503161112812.png" alt="image-20240910231514786" style="zoom: 67%;" />

C++98中，NULL仍然等于0，类型不是个指针了，匹配参数的时候自动匹配上int了，不太合理，在C++11中引入了一个新空指针

nullptr，这个空指针就不等于0了，类型为void* ，大小与 (void*)0 相同。

