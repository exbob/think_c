#第五章 有返回值的函数

##5.1 返回值

我们用过的一些内置函数，例如数学函数，会产生结果。那么，调用这样的函数就会生成一个新的值，通常要把它赋给一个变量，或作为一个表达式的一部分。例如：

	double e = exp(1.0);
	double height = radius * sin(argle);

但是，目前为止我们写过的都是 **void** 函数；也就是没有返回值的函数。调用 void 函数时，通常是独占一行，没有赋值语句：

	PrintLines(3);
	Countdown(n-1);

在这一节，我们将写的带有返回值的函数，我把它叫做有返回值的函数。第一个例子是 **area**，带一个 double 型参数，并返回给定半径的圆的面积：

	double Area(double radius)
	{
		double pi = acos(-1.0);
		double area = pi * redius * redius;
		return area;
	}

首先要注意的是，这个函数定义的开头不一样了。这里用 **double** 代替了原先的 **void** ，它指出这个函数的返回值的类型是 **double** 。

其次，函数最后一行的 **return** 语句包含了一个值。这条语句的意思是，“立即从这个函数返回，并且用后面的表达式作为返回值”。这个表达式可以写的非常复杂，所以，这个函数可以写的更简洁：

	double Area(double radius)
	{
		return acos(-1.0)*radius*radius;
	}

但是，像 **area** 这样的临时变量通常更容易调试。任何情况下，**return** 语句中的表达式的类型必须和该函数的返回值类型一致。换一句话说，当你声明的返回值类型是 **double** ，就意味着你承诺这个函数最终会产生一个 **double** 。如果你尝试在 **return** 中不带表达式，或者带一个错误类型的表达式，编译器会提醒你。

有时候，需要有多个 return 语句，根据情况返回不同的值：

	double AbsoluteValue(double x)
	{
		if(x<0)
		{
			return -x;
		}
		else
		{
			return x;
		}
	}

因为这些 return 语句在一组 if 语句中，所以只有一条会被执行。虽然在一个函数中写多条 return 语句是合法的，但是你应该记住，只要执行了一条，该函数后面的语句就都不会执行了。

位于 **return** 语句之后的代码，或者其他永远不会被执行的代码，叫做 **dead code** 。有些编译器会对此给予警告。

如果把 return 语句放在了条件语句中，一定为每一种可能出现的情况写一条 return 语句。例如：

	double AbsoluteValue(double x)
	{
		if(x<0)
		{
			return -x;
		}
		else if(x>o)
		{
			return x;
		}					/* WRONG!! */
	}

这个程序是错误的，因为如果 x 为 0，两个条件都不符合，函数最终不会执行 return 语句。不幸的是，很多 C 编译器不会注意这个错误。结果，这个程序可以编译并运行，但是当 x==0 的时候，返回值就是不确定的，在不同的环境下可能是不同的值。

现在，你可能讨厌看到编译器错误，但是这样可以获得更多的经验，你会明白，比编译器报错更坏的事情是编译器无法发现程序的错误。

会发生这样的事情：你用很多不同的 x 值来测试 **AbsoluteValue** 都工作正常。然后你把程序交给了其他人，他们在另一个环境中运行它。结果很奇怪的运行失败了，并且花了好几天来调试，最终发现问题在 **AbsoluteValue** 。如果编译器警告过你就不会这样了！

今后，如果编译器指出了一个程序中的错误，不要责怪编译器。相反，你应该感谢编译器发现了错误，并且要不遗余力的去调试。有些编译器提供一个选项，可以让编译器严格的检查并报告所有能够找到的错误。你应该一直开启这个选项

随便说一下，math 库中有一个函数叫做 **fabs** ，用于计算一个 **double** 数的绝对值。

##5.2 程序开发

本节你可以看到完整的 C 函数和它们的作用。但是不会像前面那么详细地介绍编写步骤。我会推荐一种技巧，我把它称作 **逐步开发**。

举个例子，假设你想要通过两个点的坐标 (x1,y1) 和 (x2,y2) 计算它们之间的距离，通常这样计算：

![5-1.jpg](http://imgout.ph.126.net/14671045/5-1.jpg)

第一步要考虑的是 C 语言的 **Distance** 函数应该是什么样的。换句话说，输入（参数）是什么，输出（返回值）是什么。

本例中，两个点是参数，那就需要四个 **double** 数来表示。返回值是距离，应该是 **double** 型。

现在可以写出这个函数的大致轮廓：

	double Distance(double x1, double y1, double x2, double y2)
	{
		return 0.0;
	}

**return** 语句是一个浮点数，所以函数可以通过编译并返回，虽然答案并不正确。现在这个函数还没有任何用处，但是可以试着编译它，检验有没有什么语法错误。

为了测试这个新函数，我们要用简单的值来调用它。可以在 **main** 函数中添加：

	double dist = Distance(1.0,2.0,4.0,6.0);
	printf("%f\n",dist);

选这几个值的话，水平距离是 3 ，垂直距离是 4 ，结果就应该是 5 。当你测试一个函数的时候，你应该事先知道正确的结果是什么。

一旦我们检查完函数的语法，就可以开始逐行添加代码。每次修改后，就编译运行一次。这样的话，当出现错误时，我们就知道问题肯定在最后一次添加的代码中。

第二步是计算 x2-x1 和 y2-y1 。我将这两个计算的结果储存在变量 dx 和 dy 中。

	double Distance(double x1,double y1,double x2,double y2)
	{
		double dx = x2 - x1;
		double dy = y2 - y1;
		printf("dx is %f\n", dx);
		printf("dy is %f\n", dy);
		return 0.0;
	}

我添加了输出语句，方便检查计算结果。前面已经提到，它们的结果应该是 3.0 和 4.0。

写完函数后，我会删除输出语句。像这样的代码叫做 **脚手架** ，因为它有助于编写程序，但又不是最终程序的一部分。如果稍后还需要 **脚手架** 代码，可以只将其注释，而不删除。

下一步就是计算 dx 和 dy 的平方。我们可以使用 **pow** 函数，但是直接自身相乘会更快更简单。

	double Distance(double x1,double y1,double x2,double y2)
	{
		double dx = x2 - x1;
		double dy = y2 - y1;
		double dsquared = dx*dx + dy*dy;
		printf("d_squared is %f\n", dsquared);
		return 0.0;
	}

同样的，我们可以在这里编译运行程序，检查结果（应该是 25.0）。

最后，我们可以用 **sqrt** 函数计算并返回结果。

	double Distance(double x1,double y1,double x2,double y2)
	{
		double dx = x2 - x1;
		double dy = y2 - y1;
		double dsquared = dx*dx + dy*dy;
		double result = sqrt(dsquared);
		return result;
	}

然后在 **main** 函数中输出并检查结果。

当你获得更多的编程经验时，就可以一次编写并调试多行代码。这样逐步开发的过程可以节省大量的调式时间。

这个过程的关键是：

* 由一个可工作的程序开始，逐步做小的修改。任何时候，如果出现了错误，你都知道错误的位置。
* 使用临时变量保存每一步的值，这样就可以输出并检查它们。
* 一旦程序可以正常工作了，你就可以删除一些 **脚手架** ，或合并多个语句，但是不要影响程序的可读性。

##5.3 组合

让你所愿，一旦定义了一个新函数，就可以在表达式中使用它，并且可以用已经存在的函数来构建新的函数。例如，已知两个点：圆心和圆周上的一点，求圆的面积。

我们将圆心用变量 xc 和 yc 表示，圆周上的点用 xp 和 yp 表示。第一步就是得到圆的直径，也就是这两点的距离。幸运的是，我们已经有了一个函数，**Distance**，所以直径就是：

	double radius = Distance(xc,yc,xp,yp);

第二步是计算通过直径计算圆的面积，并返回：

	double result = Area(radius);
	return result;

把上面的结合为一个函数就是：

	double AreaFromPoints(double xc,double yc,double xp,double yp)
	{
		double radius = Distance(xc,yc,xp,yp);
		double result = Area(radius);
		return result;
	}

临时变量 **radius** 和 **area** 有助于开发和调试，但是确定程序可以工作后，就可以让函数更简洁：

	double AreaFromPoints(double xc,double yc,double xp,double yp)
	{
		return Area(Distance(xc,yc,xp,yp));
	}

##5.4 布尔值

到目前为止，我们看到的数据类型都可以保存很大的值。包括很多整数，更多的是浮点数。相对而言，字符就小了很多。很多计算机语言使用的基本类型甚至更小。它叫做 **__Bool**(布尔)，只有两个值：**true**（真） 和 **false**（假） 。

不幸的是，早期的 C 语言标准没有将布尔当做一种专门的类型，而是用整数值 0 和 1 来代替。0 表示 **false** ，1 表示 **true** 。特别要指出，C 语言把所有非 0 的整数都当做 **true** 。如果你通过与 1 比较来判断一个数是否为 **true**，就会出错。

暂且不讲这个问题，我们会在下一章使用布尔值。**if** 语句中的条件就是一个布尔表达式。比较运算符的结果就是布尔值。例如：

	if(x == 5)
	{
		/* do something */
	}

运算符 **==** 比较两个整数，然后产生一个布尔值。

C99 标准没有为 **true** 和 **false** 提供关键字。很多程序都用 C 语言的宏定义来代替。例如：

	#define FALSE 0
	#define TRUE 1
	...
	if(TRUE)
	{
		/* will be always executed */
	}

如果用在循环语句，程序会一直执行（除非遇到 **return** 或 **break** 语句）。

##5.5 布尔变量

C 语言没有直接支持布尔值，所以我们不能声明布尔类型的变量。程序员通常用 **short** 型变量结合宏定义来存放真值。

	#define FALSE 0
	#define TRUE 1
	...
	short fred;
	fred = TRUE;
	short testResult = FALSE

第一行是一个简单的变量声明；第二行是一个赋值，第三行是一个声明和赋值的组合，称作初始化。

前面已经提过，比较运算符的结果就是布尔型，所以可以存放到一个变量

	short evenFlag = (n%2 == 0);    /* true if n is even */
	short positiveFlag = (x > 0);   /* true if x is positive */

然后把它作为条件语句的一部分来使用：

	if(evenFlag)
	{
		printf("n was even when I checked it");
	}

这样使用的变量叫做 **flag** ，因为它标示了条件的真假。

##5.6 逻辑运算符

C 语言有三种 **逻辑运算符**：AND，OR 和 NOT，非别用 && ，|| 和 ! 表示。这些运算符的含义和它们的英文意思类似。例如 **x>0 && x<10** ，如果 x 大于 0 并且（AND）小于 10 ，这个表达式就为真。

如果 **evenFlag || n%3 == 0** 中有一个条件为真——也就是说，如果 **evenFlag** 为真或者（OR）n 可以被 3 整除——则表达式为真。

最后，**NOT** 运算符表示否定，它可以翻转一个布尔表达式，所以，如果 **evenFlag** 为假，**!evenFlag** 就为真，即 n 为奇数。

逻辑运算符通常提供了一种简单的嵌套条件语句的方法。例如，怎样只用一个条件语句就完成下面的代码：

	if(x>0)
	{
		if(x<10)
		{
			printf("x is a positive single digit.\n");
		}
	}

##5.7 布尔函数

布尔函数就是返回布尔型数据的函数。它对于函数内部进行测试非常方便。例如：

	int IsSingleDigit(int x)
	{
		if(x>=0 && x<10)
		{
			return TRUE;
		}
		else
		{
			return FALSE;
		}
	}

这个函数叫做 **IsSingleDigit**，听起来就像是一个 yes/no 的问题。返回值的类型是 **int** ，依然遵循 0 表示 **false** 和 1 表示 **true** 的规则。每一条 return 语句都要遵循这个惯例，我们用的是宏定义。

代码本身是很简单的，虽然它有一点多。别忘了表达式 **x>=0 && x<10** 的结果是布尔值，所以可以直接将其返回，无需 if 语句：

	int IsSingleDigit(int x)
	{
		return (x>=0 && x<10);
	}

在 main 函数里直接调用：

	printf("%i\n", IsSingleDigit(2));
	short bigFlag = !IsSingleDigit(17);

第一行会输出 **true** 的值，因为 2 是一个单数。不幸的是，C 语言输出的布尔值不是单词 **TRUE** 和 **FALSE** ，而是整数 1 和 0 。

第二行会将 **true** 的值赋给 **bigFlag** ，因为 17 不是一个正的单数。

布尔函数常用在条件语句里

	if(IsSingleDigit(x))
	{
		printf("x is little\n");
	}
	else
	{
		printf("x is big\n");
	}

##5.8 main 的返回值

我们已经学习了函数的返回值，现在可以详细的讲一下 **main** 函数的返回值。它支持返回一个整数：

	int main(void)

通常从 main 返回的是 0 ，表示程序运行成功。如果出了什么差错，通常返回 -1 ，或者用其他整数代表一种错误现象。C 语言为此提供了两个常量，**EXIT\_SUCCESS** 和 **EXIT\_FAILURE**

	int main()
	{
		return EXIT_SUCCESS;    /*program terminated successfully*/
	}

当然，你可能想要知道谁会获得这个返回值，因为我们从来没有调用过 **main** 。事实是，当操作系统执行一个程序的时候，是从调用 **main** 开始的，调用的方法和其他函数是一样的。

系统可以向 **main** 传递一些参数，稍后我们会详细介绍。

##5.9 词汇

**return type(返回类型):** 函数返回值的类型。

**return value(返回值):** 调用函数后产生的值。

**dead code(死代码):** 程序里的一部分，永远都不会被执行，通常位于一个 **return** 语句之后。

**scaffolding(脚手架):** 程序开发过程中用到的代码，但是不会出现在最终版本中。

**void(空):** 一种特殊的返回类型，表示函数没有返回值。

**boolean(布尔):** 一种值或变量，只有两个状态，通常叫做 *true* 和 *false* 。C 语言中，布尔值可以存放在 **bool** 型变量中。

**flag(标识):** 一种变量（通常是 bool 型），表示一种条件或状态。

**comparison operator(比较运算符):** 一种运算符，用于比较两个值，并产生一个表示二者关系的布尔值。

**logical operator(逻辑运算符):** 一种将布尔值组合到一起的运算符，用于测试复合条件。

##5.10 练习

**Exercise 5.1**

又是德文！！

**Exercise 5.2**

如果给你三根木棒，你能否将它们摆成一个三角形。例如，如果其中一根长 12 英寸，另外两根长 1 英寸，很明显，那两根短的无法连接到一起。对于任意三个长度，有一种简单的方法可以判断它们能否组成一个三角形：

“如果三个长度中，有一个比另外两个的和还要长，那就无法组成一个三角形。其他的情况都可以”

写一个名为 **IsTriangle** 的函数，参数为三个整数，返回值为 **true** 或 **false** ，判断给定长度能否组成一个三角形。

这个练习的目的是用条件语句写一个带返回值的函数。

**Exercise 5.3**

德文。。。

**Exercise 5.4**

德文。。。

**Exercise 5.5**

德文。。。

**Exercise 5.6**

a. 新建一个名叫 **Sum.c** 的程序，输入如下两个函数。

	int FunctionOne(int m,int n)
	{
		if(m ==n)
		{
			return n;
		}
		else
		{
			return m + FunctionOne(m+1,n);
		}
	}

	int FunctionTwo(int m,int n)
	{
		if(m == n)
		{
			return n;
		}
		else
		{
			return n * FunctionTwo(m, n-1);
		}
	}

b. 在 main 函数中写几行代码测试这两个函数。多调用几次，使用一些不同的值，看看得到怎样的结果。通过测试和研究，指出这些函数是做什么的，并为它们起一个更有意义的名字。为函数添加注释，简单地描述函数的作用。

c. 在函数的开始处添加 **printf** 语句，打印参数的值。这是一个有用的调试技巧，这样可以展示程序的执行过程。

**Exercise 5.7**

（这个练习基于 Abelson 和 Sussman 的 *Structure and Interpretation of Computer Pragrams* 的 44 页）

下面的算法出现在 Euclid 的 Elements (Book 7,ca. 300 B.C.)。它可能是最古老的不平凡算法。

这个算法基于这样的情况，如果 r 是 a 除以 b 的余数，那么 a 和 b 的公因子等于 b 和
r 的公因子。因此可以用等式

	gcd(a,b) = gcd(b,r)

将计算一对整数的 GCD 的问题简化为计算越来越小的整数的 GCD 。例如，

	gcd(36,20) = gcd(20,16) = gcd(16,4) = gcd(4,0) = 4

所以 36 和 20 的 GCD 是 4 。它可以从任意一对数字开始，逐步减小，直到第二个数为零，这时第一个数就是这对数的 GCD 。

写一个名为 **gcd** 函数，带两个整型参数，使用 Euclid 的算法，计算并返回两个数的最大公因子(GCD)。

##笔记

* so far ：目前为止
* in either case ：在任一情况下
* make a promise ：做一个承诺
* sick of ：腻烦
* from now on ：今后
* if only ：但愿，只要
* agreement ：协议，约定
* convention ：惯例
* positive ：正
* negative ：负
* remainder ：余数
* greatest common divisor ：最大公因子
