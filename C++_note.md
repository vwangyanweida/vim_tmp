# 快速掌握一个语言的50%: C++
<font color=red></font>
<font color=green></font>
## 问题
1. 有了auto为什么还需要decltype
    1. auto只能让编译器确定变量的类型，而decltype可以确定一种类型来定义变量。

## 一. 基础
### 1. 基本类型
1. 类型转换
    1. <font color=red>当我们赋给无符号数类型一个超出它表示范围的值时，如果是初始值对无符号数类型表示的数值总数取模后的余数。</font>
    2. 可能会出现无意对无符号数赋一个负值：
        1. 当一个算术表达式既有无符号数又有int值时，那个int值就会转换成为无符号数。
                
                unsigned u = 10;
                int i = -42
                std::out << i + u << std::endl;  //如果int占32位，输出4294967264

        2. 当从无符号数中减去一个值时，不管这个值是不是无符号数，我们都必须确保结果不能是一个负值：

                unsigned u1 = 42， u2 = 10；
                std::cout << u1 - u2 << std::endl;  // 正确：输出32
                std::cout << u2 - u1 << std::endl;  // 正确：不过输出是结果取模

        3. 编译器不管结果的类型，只遵循类型转换，计算时，强制转换类型，然后把值当做无符号数，可能出错

        4. 无符号数不会小于0，这一事实同样关系到循环的写法。

                for (unsigned u = 10; i >= 0; i--)
                    ...
                // 错误，unsigned永远不会小于0

        5. 总结切勿混用带符号数和无符号数
            > 如果表达式里既有带符号数和无符号类型，带符号数会自动转化为无符号数，结果也是无符号数类型，如果出现负数，则出错。

2. 变量
    1. 初始化不是赋值，初始化的含义是创建变量时赋予其一个初始值，而赋值的含义是把对象的当前值擦除，而以一个新值来替代。
    2. 初始化
        - int units_sold = 0;
        - int units_sold = {0}; 
        - int units_sold(0);
        - int units_sold{0};

    3. 列表初始化：
        1. 当用于内置类型的变量时，这种初始化形式有一个重要特点：如果我们使用列表初始化且初始值存在丢失信息的风险，则编译器报错。
            
                long double = 3.1415926;
                int a{ld}, b = {ld};  //错误：转换未执行，因为存在丢失信息的危险。
                int(ld), d = ld;

            1. <font color=red>列表初始化可能是一个精确初始化，不可以以丢失精确度的代价来强制转换。</font>

    4. 声明

	5. const 限定符
		1. const 对象尽在文件内有效,除非声明和定义都加上extern
		2. 引用类型必须与其所对引用的对象类型一致,但有两个例外:
			1. const int &i = (double) 2.3
				1. 在初始化常量引用时允许任意表达式作为初始值,只要该表达式的结果能转换为引用类型即可
				2. 
					1. 例子
					
							double dval = 3.14;
							cons int &ri = dval;

							const int temp = dval;
							const int &ri = temp;

3. 复合类型
    1. 引用
        1. 定义引用时，程序会把引用和它的初始值绑定在一起，而不是将初始值拷贝给引用。一旦初始化完成，引用将和它的初始值绑定在一起。
        2. 因为无法令引用重新绑定到另外一个对象，因此引用必须在定义的时候初始化。
        2. 引用只是一个别名，而不是对象。它的值应该是会被编译器编译为对象的地址，指针是一个单独的空间存储指向对象的地址，而引用本身应该就会解释成对象的地址，没有存储空间。

    2. 指针
        1. void *是特殊的指针类型，可用于存放任意对象的地址。
        2. void *可用的方法有限，因为不确定它的类型，所以类型的方法都不能用。
            1. 拿它和别的指针比较
            2. 作为函数的输入或输出
            3. 赋值给另外一个void * 指针
            4. 不能直接操作void *指针所指的对象，因为我们不知道这个对象是什么类型，可以做那些操作。

    3. 总结
        1. 引用本身不是一个对象，因此不能定义指向引用的指针，但指针是对象，所以存在对指针的引用。
            
                int i = 42;
                int *p;
                int *&r = p;  //r是一个对指针p的引用。

                r = &i;

                // *、&都是类型修饰符，不是类型，只作用于后面的变量名上。

        2. 从右向左阅读r的定义，离变量名最近的符号对变量的类型有最直接的影响。

4. const
    1. const 对象一旦创建后不能改变，所以const对象必须初始化。
    2. 默认情况下，const对象仅在文件中有效
        1. 如果要共享变量，办法是：对于const变量不管是声明还是定义都添加extern关键字。

    3. const和指针
        1. 顶层const表示指针本身是一个常量
        2. 地城const表示指针所指对象是一个常量。

5. constexpr 和常量表达式
    1. 常量表达式
        1. 常量表达式：值不会改变并且在变异过程就能得到结果的表达式。
        2. 一个对象是不是常量表达式 由它的数据类型和初始值共同决定。
        3. 字面值属于常量表达式、常量表达式初始化的const对象也是常量表达式。

    2. constexpr
        1. C++11：允许将变量声明为 constexpr类型以便由编译器来验证变量的值是否是一个常量表达式。
        2. constexpr声明的变量一定是常量，而且必须用常量表达式来初始化

                constexpr int mf = 20;
                constexpr int limit = mf + 1；
                constexpr int sz = size();  //只有当size是一个constexpr函数时，才是一个正确的声明语句。

        3. 字面值类型：算术类型、引用、指针

        4. 一个constexpr指针的初始值必须是nullptr或者0，或者是存储于某个固定地址中的对象。
            1. 函数体内定义的变量一般来说并非存放在固定的地址中，因此constexpr不能指向这样的变量。
            2. 定义于所有函数体之外的对象其地址固定不变，能用来初始化constexpr指针。
            3. constexpr也能绑定到有效范围超出函数体本身的变量，它们有固定的地址。

        5. constexpr 声明中如果定义了一个指针，限定符constexpr仅对指针有效，与指针所指的对象无关。

                const int *p = nullptr;      //  p是一个指向整形常量的指针
                constexpr int *q = nullptr;  //  q是一个指向整数的常量指针。

6. 处理类型
    1. 类型别名
        1. typedef
        2. using SI = Sales_item;
        3. 如果某个类型别名指代的是复合类型或常量，可能出错。
            1. 遇到类型别名，不能简单的把类型别名替换成它本来的样子。

                    typedef char *pstring;
                    const pstring cstr = 0;
                    const pstring *ps;

                    const char *cstr = 0   //是对const pstring cstr错误的理解。

                    声明语句中用到pstring时，基本数据类型是指针。而用char * 重写了声明语句后，数据类型就变成了char， * 成为了声明符的一部分。
        
    2. auto
        > 让编译器替代我们去分析表达式所属的类型。

    3. decltype
        > 选择并返回操作数的数据类型。编译器分析表达式并得到它的类型，却不实际计算表达式的值。

        1. decltype 处理顶层const 和引用的方式和auto不同

                const int ci = 0, &cj = ci;
                decltype(ci) x = 0;
                deltype(cj) y = x; // y的雷兴师const int&,y绑定到x
                deltype(cj) z; // 错误，z是一个引用必须初始化

        2. 如果decltype使用的表达式不是一个变量，则decltype返回表达式结果对应的类型。
            
                int i = 42, *p = &i, &r = i;
                decltype(r + 0) b;
                decltype（*p）c;   //错误：c是int&，必须初始化。 

            1. 如果表达式的内容是解引用操作，则decltype返回引用类型。 **因为解引用是读的指针中保存的地址的变量的值，是那个变量的地址。
                我们猜测过引用就是把引用名解释成和变量一样的地址。这里返回变量的地址，和引用一样的。**

        3. decltype和auto的另一处重要区别是，decltype的结果类型与表达式形式密切相关。

        4. decltype所有的表达式如果变量名加上一对括号的区别。
            1. 不加括号，得到的结果就是该变量的类型
            2. 加括号，编译器把它当做是一个表达式。变量是一种可以作为赋值语句左值的特殊表达式，所以这样的decltype就会得到引用类型。

                    decltype((i)) d;   // 错误，d是int&，必须初始化。
                    decltype(i) e;     // 正确：e是一个未初始化的int 

                1. 切记：decltype((variable)) (双层括号) 的结果永远是引用，而decltype(variable) 结果只有当variable本身就是一个引用时才是引用。

### 2. 基本语法

1. 强制类型转换
    1. 形式：  cast-name<type>(expression);
    2. cast-name:
        1. static_cast
            > 任何具有名确定义的类型转换，只要不包括底层const，都可以使用static_const。

        2. dynamic_cast
            > 运行时类型识别

        3. const_cast
            > const_cast 只能改变运算对象的底层const

            1. 去掉const性质
                    
                    const  char *pc;
                    char *p = const_cast<char *>(pc);   //正确：但是通过p写值是未定义的行为。

            2. const_cast不能改变表达式的类型，这时static_cast来改变的。const_cast能改变表达式的常量属性。

        4. reinterpret_cast
            > 通常为运算对象的位模式提供较低层上的重新解释。

            1. eg

                    int *ip;
                    char *pc = reinterpret_cast<char *>(ip);

                    我们必须牢记pc所指的真实对象是一个int而非字符，如果把pc当成普通的字符指针使用就可能在运行时发生错误。

                    string str(pc) // 或导致运行时错误。

2. 循环

    1. switch case中
        1. case标签必须是整形常量表达式。

                char ch = getVal();
                int ival = 34;
                switch(ch){
                    case 3.24; //错误：case标签不是一个整数
                    case ival; //错误：case标签不是一个常量
                }

        2.  标签不应该孤零零出现，它后面必须跟上一条语句或者另外一个case标签。

    2. for 语句中的init-statement可以定义多个对象，但是只能有一条声明语句，因此，所有变量的基础类型必须相同。相当于一个类型定义了多个变量。

            for (decltype(v.size()) i = 0, sz = v.size(); i != sz; ++i)
                v.push_back(v[i]);

    3. 范围for
        > C++11定义，可以遍历容器或其他序列的所有元素

        1. 形式
                
                for (declaration : expression)
                    statement
        
        2. expression:必须是一个序列，比如用花括号括起来的初始值列表、数组、vector、string等，这些类型的共同特点是能返回迭代器的begin和end成员。

        3. declaration定义一个变量
            1. 最简单用auto类型说明符来说明变量类型。
            2. 如果需要对序列中的元素执行写操作，循环变量必须声明成引用类型。

    4. 跳转语句
        1. break；负责终止离它最近的while、do while、for或switch语句。
            > break语句只能出现在迭代语句或者switch语句内部。

        2. continue：终止最近的循环中的当前迭代并立即开始下一次迭代。

        3. goto：从goto语句无条件跳转到统一函数内的另一条语句。
            > 不要在程序中使用goto

            1. 形式： goto label；
            2. 和switch语句类似，goto语句也不能将程序的控制权从变量的作用域之外转移到作用域之内。

            > 不要在程序中使用goto

            1. 形式： goto label；
            2. 和switch语句类似，goto语句也不能将程序的控制权从变量的作用域之外转移到作用域之内。
                        > 不要在程序中使用goto

           
                        1. 形式： goto label；
                        2. 和switch语句类似，goto语句也不能将程序的控制权从变量的作用域之外转移到作用域之内。
                                    > 不要在程序中使用goto

                       
                                    1. 形式： goto label；
                                    2. 和switch语句类似，goto语句也不能将程序的控制权从变量的作用域之外转移到作用域之内。

3. 异常
    1. throw表达式：
        1. 形式：throw + 表达式，表达式的类型是抛出的异常类型。

                if (item1.isbn() != item2.isbn())
                    throw runtime_error("Data must refer to same ISBN");

        2. runtime_error:标准库异常类型的一种，定义在stdexcept头文件中。string对象初始化它。
        
    2. try 语句块：
        1. 形式：try 块之后是一个或多个catch子句。catch子句包括三部分：关键字 catch、括号内一个(可能是未命名)对象的声明(异常声明)、以及一个块。

                try {
                    program-statements;
                } catch (exception-declaration){
                    handler-statements;
                } catch (exception-declaration){
                    handler-statements;
                }

    3. 一套异常类，用于在throw表达式和相关的catch子句之间传递:
        1. 异常类只定义了一个名为what的成员函数，没有参数，返回只是一个指向C字符串的 const char *.

### 3. 主要语言构造

## 二.高级数据结构 
### 1. 字符串
1. 初始化
    1. 拷贝初始化
        1. 使用等号(=)初始化一个变量，实际上执行的是拷贝初始化，编译器把等号右侧的初始值拷贝到新创建的对象中去。
            
                string s2 = s1;

    2. 直接初始化
        1. 不适用等号，执行的是直接初始化

                string s2(s1);

### 2. 数组
### 3. 向量

## 三. 字符串处理：替换，查找、截断
### 1. Regex
### 2. Parser
### <font color=green>3. 字符编码</font>

## 四. 基本面向对象或函数式编程的特征
### 1. 继承
### 2. 多态
### 3. Lambda 函数

## 五. 调试：TDD技能
### 1. 异常
### 2. 错误处理
### 3. 断言
### 4. 日志
### 5. 调试支持
### <font color=green>6. 单元测试</font>

## <font color=green>六. 运行时</font>
### 1. 程序代码和可执行代码的组织机制
### 2. 运行时模块加载
### 3. 符号查找机制

## 七. 流
### 1. 基本输入输出
### 2. 文件处理
### 3. 输入输出流类的组织

## 八. 事件驱动
### 1. callback 方法调用
### 2. 如何支持事件驱动编程模型
1. .NET 的delegate，Java的anonymous inner class，Java 7的closure，C++OX的tr1::function/bind……等各种语言都会在这里下足功夫。

## 九. 序列化和反序列化
### 1. 缺省机制

## 十. regex和XML处理问题，可以跳过

## 十一. 并发
### 1. 线程
### 2. 进程
### 3. 并发
### 4. 异步调用

## 十二. <font color=green>动态编程</font>
### 1. 反射
### 2. 元数据编程
### 3. 数据和程序之间的相互转化机制
### 4. 运行时编译和执行的机制

## 十三. 语言常用的特色feature

## 十四. 闲暇考虑 
### 1. 这个语言有哪些惯用法和模式 
### 2. 这个语言的编译/解释执行机制
### 3. 泛型

## 十五. C/C++ 需要考虑内存模型

## 十六. 之后
### 1. 数据库 
### 2. 网络
### 3. 图形
### 4. 框架
### 5. 库

