## 问题
1. 有了auto为什么还需要decltype
    1. auto只能让编译器确定变量的类型，而decltype可以确定一种类型来定义变量。

2. 字面值 "hello " 是什么类型，string还是 const char *。
3. mutable 使用场景。

4. map 下标运算返回的类型是mapped_type，那直接对下标的++，是怎么运算的，mapped_type可以直接进行算术运算吗？
5. 迭代器返回的是指针还是引用，不是拷贝。
6. return hash<string> () (sd.isbn())  什么意思

## 基本类型
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

## 基本语法
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

4. 返回数组指针的函数：
    1. 指向数组的指针： int （*p）[10]
    2. 声明一个数组类型的别名： typedef int array[10];  
    3. <font color=green>因为数组的定义是：类型 + 变量名 + [], 所以，数组的类型相当于 int []. 剩下的直接修饰变量名的类型，和顶层const指针有点像</font>
    4. 返回数组指针类型的函数定义：
        1. 使用类型别名：

                typedef int arrT[10];  // arrT是一个类型别名，它表示的类型是含有10个整数的数组。
                using arrT = int [10]; // arrT的等价声明
                arrT *func(int i);     // func 返回一个指向含有10个整数的数组的指针。

        2. 声明一个返回数组指针的函数
            1. 如果想在声明func时不使用类型别名，我们必须牢记被定义的名字后面的数组的维度：

                    int arr[10];
                    int *p1[10];
                    int （*p2）[10] = &arr;  //编译器必须知道这个指针可以指向的内存范围。

            2. 形式： Type （*function(parameter_list)）[dimension]

                    int （*func(int i)）[10];

        3. 尾置返回类型
            > C++11: 任何函数类型定义都可以使用尾置返回，但对于复杂函数最有效

            1. 形式
                1. 尾置返回类型跟在形参列表后面并以一个 -> 符号开头。
                2. 为了表示函数真正的返回类型跟在形参列表之后，我们在本应该出现返回类型的地方放置一个auto：

                        auto func(int i) -> int （*）[10];

        4. 使用decltype
            > 如果我们知道函数返回的指针将指向哪个数组，就可以使用decltype关键字声明返回类型。

            1. eg

                    int odd[] = {1, 3, 5, 7, 9};
                    int even[] = {0, 2, 4, 6, 8};
                    
                    decltype(odd) *arrPtr(int i){
                        return (i % 2) ? &odd : &even;
                    }

            2. decltype(odd) 返回的是一个数组而不是指针，所以定义时，需要自己加上*。

5. 函数重载
    > 同一作用域内的几个函数名字相同但形参列表不同，我们称之为 重载函数(overloaded). 只能是形参不同，因为编译器需要知道调用时调用的那个函数，只能通过实参列表和形参来匹配。 

    1. 对于重载函数来说，它们应该在形参数量或者形参类型上有所不同。
    2. 不允许两个函数除了返回类型外其他所有的要素都相同，类型别名也属于相同类型，编译器不能根据实参判断出是原型还是类型别名。
    3. const形参，函数调用时会忽略顶层const，所以底层const形参也不能作为重载函数区别。一个拥有顶层const的形参和另一个没有顶层const的形参无法区别开。
    4. 但是如果形参是某种类型的指针或引用，则通过区分其指向的是常量对象还是非常量对象可以实现函数重载，此时的const是底层的。
    5. 尽管函数重载能在一定程度上减轻我们函数起名的负担，但最好只重载那些非常相似的操作。

    5. 函数匹配/重载确定
        1. 最佳匹配
        2. 无匹配
        3. 二义性调用

    6. 重载和作用域：
        1. C++中，名字查找在类型检查之间，如果编译器在当前作用域中找到了所需的名字，编译器就会忽略外层作用域中的同名实体。

6. 默认参数
    1. 默认参数必须在参数列表末尾，并且默认参数之后都必须有默认实参。
    2. 用作默认实参的名字在函数声明所在的作用域内解析，而这些名字的求职过程发生在函数调用时：
        
            sz wd = 80;
            char deff = ' ';
            sz ht();
            string screen(sz = ht(), sz = wd, char = def);

            void f2(){
                    def = '*';         // 改变了默认实参的值。 注意，这里是重定义了def的值，而不是重新声明一个新的变量，所以该变了外部变量的值。
                sz wd = 100;       //隐藏了外层的wd，但是没有改变默认值。  注意，这里是重新定义了一个新的变量，作用是在这里隐藏了外部的变量，但默认实参在定义的地方确定了实参的值。
                window = screen(); //调用了screen(ht(), 80, '*')
            }

7. 函数类型
    1. using F = int (int *, int);   // F是函数类型，不是指针
    2. using PF = int（*）(int*, int); // PF是指针类型

    3. 和数组类似，函数内不能反悔一个函数或指针，但是可以返回指向函数类型或数组类型的指针。
    4. 返回函数类型时，必须把返回类型写成指针形式，编译器不会自动将函数返回类型当做成对应的指针类型处理。

    5. eg

            PF f1(int);   //正确
            F f1(int);    // 错误，F是函数类型，f1不能返回一个函数
            F *f1(int);   // 正确，显示的指定返回类型是指向函数的指针。

            int （*f1(int)）(int*, int);

            auto f1(int) -> int （*）(int*, int);

    6. 如果明确知道返回的函数是哪一个，可以使用decltype来简化返回函数指针的函数定义。

## 字符串
1. 初始化
    1. 拷贝初始化
        1. 使用等号(=)初始化一个变量，实际上执行的是拷贝初始化，编译器把等号右侧的初始值拷贝到新创建的对象中去。
            
                string s2 = s1;

    2. 直接初始化
        1. 不适用等号，执行的是直接初始化

                string s2(s1);
## 动态内存
1. 动态内存和智能指针
    0. 动态内存是通过一对运算符来完成的：
        1. new ：在动态内存中为对象分配空间并返回该对象的指针，可以选择为它进行出世哈
        2. delete : 接受一个动态对象的指针，销毁该对象，并释放与之关联的内存。

    1. shared_ptr类
        1. 只能指针也是模板，所以申请时需要指明指向的类型： shared_ptr<string> p1; shared_ptr<list<int>> p2;
        2. 默认初始化的智能指针中保存着一个空指针
        3. 智能指针的使用和普通指针类似，解引用一个智能指针返回它所指的对象
        4. 如果在条件判断中使用一个智能指针，效果就是检测它是否为空。
        5. 操作
            1. shared_ptr和uniue_ptr都支持的操作
                1. shared_ptr<T> sp
                2. unique_ptr<T> up   空智能指针
                3. p                  条件判断，若p指向一个对象，则为true
                4. *p
                5. p->mem
                6. p.get()           返回p中保存的指针，要小心使用，若智能指针释放了起对象，返回的指针所指的对象也就消失了，应该可以和弱引用相互使用。
                7. swap(p, q)        交换p和q中的指针
                8. p.swap(q)

            2. shared_ptr独有的操作
                1. make_shared<T> (args)   :返回一个shared_ptr对象，指向一个动态分配的T类型对象，使用args来初始化此对象
                2. shared_ptr<T> p(q) :    p是shared_ptr q的拷贝，此操作会递增q中的计数器，q中的指针必须能转换为T*
                3. p = q;
                4. p.unique()
                5. p.use_count()

        6. make_shared函数
            > 定义在memory头文件中
                
            1. shared_ptr<int> p3 = make_shared<int>(42);
            2. 类似于容器的emplace函数，make_shared 用其参数来构造给定类型的对象。

        7. shared_ptr 的拷贝和赋值

        8. shared_ptr自动销毁所管理的对象

        9. shared_ptr 还会自动释放相关联的内存
            
    2. 直接管理内存
        1. new分配和初始化对象
            1. 自由空间分配的内存是无名的，因此，new无法为其分配的对象命名，而是返回一个指向该对象的指针
            2. 默认情况下，动态分配的对象是默认初始化的：内置类型或组合类型值将是未定义的，类类型是默认构造函数
            3. 直接初始化
                1. int *pi = new int(1025)
                2. string *p = new string(10, '9')
                3. vector<int> *pv = new vector<int>{0, 1, 2};

            4. 也可以对动态分配的内存值初始化：只需在类型名之后跟一对空括号即可
                1. string *ps1 = new string() / string *ps2 = new string;
                2. 值初始化后，就可以使用auto来判断申请的类型，但是编译器需要用初始化的类型来推断分配的类型，所以括号里只能有一个初始化器时才可以

            5. 动态分配的const对象

            6. 内存耗尽
                1. 程序的内存耗尽，new会失败，会抛出一个类型为 bad_alloc的异常，可以用 nothrow 来组织它抛出异常

                        int *p = new int;
                        int *p = new (nothrow) int;   // 如果分配失败，new返回一个空指针。

            7. 释放内存
                1. delete表达式：delete 接受一个指针，指向我们要释放的对象
                
            8. 动态对象的生存期直到被释放时为止

            9. 动态内存管理容易出错
                1. 忘记delete内存
                2. 使用已经释放掉的对象
                3. 同一块内存释放两次。

            10. delete 之后要重置指针值
                1. **delete一个指针后，指针值就无效了，就是所谓的空悬指针**
                2. 避免空悬指针的方法：
                    1. 在指针离开其作用域之前释放掉它所关联的内存，可以避免空悬指针。
                    2. 如果需要保留指针，可以在delete之后，将nullptr赋予指针。

    3. shared_ptr和new结合使用
        1. 如果不初始化一个智能指针，他就会被初始化为一个空指针
        2. 接受指针参数的智能指针构造函数是explicit的，因此，不能将一个内置指针转换为一个智能指针，必须使用直接初始化形式来初始化一个智能指针

                shared_ptr<int> p1 = new int(1024);  //错误，必须使用直接初始化形式
                shared_ptr<int> p1(new int(1024));   //正确
    
                shared_ptr<int> clone(int p){
                    return new int(p);    //错误，隐式转换为shared_ptr<int>
                    return shared_ptr<int>(new int(p));
                }

        3. 智能指针是一个独特的类型，记住第2点，它的构造函数是explicit的，不能讲普通指针隐式转换为智能指针，因为没有申请对象的引用计数对象。new返回的是普通指针。也就是说，普通指针只能作为智能指针的构造函数的参数，不能拷贝赋值。

        4. 默认情况下，初始化智能指针的普通指针必须指向动态内存，因为智能指针默认使用delete释放它所关联的对象。
        5. 但是也可以将智能指针绑定到一个指向其他类型的资源的指针上，但这样做，**必须提供自己的操作来代替delete。**

        6. 定义和改变shared_ptr的其他方法
            1. shared_ptr<T> p(q);
            2. shared_ptr<T> p(u);   将u置为空。
            3. shared_ptr<T> p(q, d);   d是代替delete的。p接管了内置指针q所指对象的所有权
            4. shared_ptr<T> p(p2, d);   d是代替delete的。p接管了shared_ptr p2所指对象的所有权
            5. p.reset()
            6. p.reset(q)           p不再指向原来的内存，接受一个内置指针的参数q。注意，p被设为空指针，如果没有q，并且如果p是唯一的shared_ptr，对象会被删除。d代替delete
            7. p.reset(q, d)

        7. 不要混合使用普通指针和智能指针
            1. 使用一个内置指针来访问一个智能指针所负责的对象是很危险的，因为我们无法知道对象何时会被销毁。

        9. 也不要使用get初始化另一个智能指针或为智能指针赋值。
            1. 智能指针的get函数返回一个内置指针，指向智能指针管理的对象
            2. 场景：
                1. 需要向不能使用智能指针的代码传递一个内置指针
                2. 使用get返回的指针的代码不能delete此指针。

            3. get指针来将指针的访问权限传递个代码，确定代码不会delete指针的情况下，才能用get。永远不要get初始化另一个智能指针或为另一个智能指针赋值。

        10. 其他shared_ptr操作
            1. reset将一个新指针赋值给一个shared_ptr：
                1. p = new int(1024);  //错误
                2. p.reset(new int(1024));
            2. shared_ptr像指向一个内置指针，一是初始化式接受内置指针的参数，二是reset接受内置参数。

    4. 智能指针和异常
        1. 使用智能指针，即便程序块过早释放结束，智能指针类也能确保在内存不再需要时将其释放。而内置指针在异常发生时很可能没有释放资源。
        2. 智能指针和哑类
            1. 分配了资源，而又没有定义析构函数来释放这些资源，可能会遇到与使用动态内存相同的错误，没有释放资源。在资源分配和资源释放之间发生了异常，也会发生资源泄露。
            2. 以shared_ptr指向分配的资源，以自定义的释放资源的类来代替shared_ptr的delete函数。

        3. 智能指针陷阱：
            1. 不适用相同的内置指针初始化或reset多个智能指针
            2. 不delete get()返回的指针
            3. 不适用get() 初始化或reset另一个智能指针。
            4. get()返回的指针，当最后一个对应的智能指针销毁后，就无效了
            5. 如果智能指针管理的资源不是new分配的内存，记住传递给他一个删除器。

    5. unique_ptr
        1. unique_ptr 拥有它所指的对象，unique_ptr被销毁时，它所指的对象也被销毁。
        2. 定义一个unique_ptr时，必须将它绑定到一个new返回的指针上。初始化unique_ptr和shared_ptr类似，必须采用直接初始化形式。
            
                unique_ptr<double> p1;
                unique_ptr<int> p2(new int(42));

        3. unique_ptr 独占它所指向的对象，所以unique_ptr不支持普通的拷贝或赋值操作。
            
                unique_ptr<string> p1(new string("Stegosaurus"));
                unique_ptr<string> p1(p2);             //错误，unique_ptr不支持拷贝。 
                unique_ptr<string> p3;
                p3 = p2                                  //错误，unique_ptr不支持赋值

        4. unique_ptr操作
            1. unique_ptr<T> u1 
            2. unique_ptr<T, D> u2     u2会使用一个类型为D的可调用对象来释放它的指针
            3. unique_ptr<T, D> u(d)    d相当于delete
            4. u = nullptr
            5. u.release()     u放弃指针的控制权，返回指针，并置为空
            6. u.reset()  释放对象
            7. u.reset(q)      如果提供了内置指针q，令u指向这个对象，否则将u置为空。
            8. u.reset(nullptr)

        5. 不能拷贝或赋值unique_ptr，但可以通过release或reset将指针所有权从一个unique_ptr转移给另一个。
            
                unique_ptr<string> p2(p1.release());
                unique_ptr<stirng> p3(new string("Trex");
                p2.reset(p3.release())

        6. 如果unique_ptr不为空，reset原来的对象被释放。
        7. release会切断unique_ptr和它原来管理的对象间的联系。release返回的指针通常会被用来初始化另一个智能指针或给另一个智能指针赋值。如果不用智能指针来保存release返回的指针，需要自己来delete。

                p2.release();   //错误，p2不会释放内存，并且我们丢失了指针
                auto p = p2.release()  //正确，但是我们必须自己delete(p).
        
        8. 传递unique_ptr参数和返回unique_ptr
            1. 不能靠别unique_ptr的规则有一个例外：我们可以拷贝或赋值一个将要被销毁的unique_ptr，最常见的例子是函数返回一个unique_ptr

                    unique_ptr<int> clone(int p){
                        return unique_ptr<int>(new int(p));
                        }

                    unique_ptr<int> clone(int p){
                        unique_ptr<int> ret(new int(p));
                        return ret;
                        }

        9. 向unique_ptr 传递删除器
            1. 类似于shared_ptr，unique_ptr默认情况下用delete释放它指向的对象，我们也可以重载一个unique_ptr中的默认删除器。这和shared_ptr直接初始化指定删除器不同。
            2. unique_ptr<objT, delT> p(new objT, fcn);

    6. weak_ptr
        1. weak_ptr是不控制所指对象生存期的智能指针。
        2. 指向shared_ptr管理的对象
        3. 不改变shared_ptr引用计数
        4. 操作；   
            1. weak_ptr<T> w
            2. weak_ptr<T> w(sp)
            3. w = p        shared_ptr或者weak_ptr
            4. w.reset()
            5. w.use_count()
            6. w.expired()
            7. w.lock()       如果expired为true，返回一个空的shared_ptr，否则返回一个指向w的shared_ptr
        5. 创建一个weak_ptr，必须用一个shared_ptr来初始化它。
        6. 因为对象可能不存在，所以我们不能使用weak_ptr来直接访问对象，而必须调用lock。
        7. 核查指针类

2. 动态数组
    1. new和数组
        1. 为了让new分配一个对象数组，在类型名之后跟一对方括号，指明要分配的对象的数目。
        2. new分配要求数量对象并返回指向第一个对象的指针。

                int *pia = new int[get_size()];
                方括号里大大小必须是整数，但不必是常量。

        3. 分配一个数组会得到一个元素类型的指针。
            1. new T[]分配一个数组时，我们得到的是一个数组元素类型的指针，所以不可以使用数组的方法比如 begin、end、for
            2. 动态数组是指针，而不是数组。

        4. 初始化动态分配对象的数组
            1. 默认情况下，new分配的兑现都是默认初始化的。可以进行值初始化，方法是在后面加一对空括号。

                    int *pia = new int[10];
                    int *pia = new int[10]();
            2. 还可以提供一个元素初始化器的花括号列表
                
                    int *pia3 = new int[10]{0, 1, 3, 4,5,6,6};

            3. 可以加上空括号来值初始化，但是括号里不能给出初始化器。

        5. 动态分配一个空数组是合法的
            
                char arr[0];   //错误：不能定义长度为0的数组
                char *cp = new char[0]   //正确：但cp不能解引用。

        6. 释放动态数组
            1. 为了释放动态数组，需要特殊形式的delete：在指针前加上一个空方括号对：
                    
                    delete p;    //p必须指向一个动态分配到对象或空
                    delet [] pa; //pa必须指向一个动态分配的数组或空。

        7. 智能指针和动态数组
            1. 标注库提供了一个可以管理new分配数组的unique_ptr版本

                    unique_ptr<int[]> up(new int[10]);
                    up.release();   //自动调用delete [] 销毁其指针。   注意，unique_ptr的release本身不会释放资源的，不能用这个方法释放指针。但是动态数组似乎重载了方法。

            2. 当一个unique_ptr指向一个数组时，不能用点和箭头成员运算符，但是可以用下标运算符。

            3. shared_ptr不支持管理动态数组。如果一定要使用shared_ptr管理动态数组，必须自己提供定义的删除器。

                    shared_ptr<int> sp(new int[10], [](int *p) {delete[] p;});
                    sp.reset()
            4. shared_ptr不直接支持动态数组管理会影响我们访问数组元素

                    for(size_t i = 0; i != 10; i++)
                        *(sp.get() + i) = i;  

                    shared_ptr未定义下标运算符，而且智能指针类型不支持指针算术运算，因此必须使用get来获取一个内置指针。

    2. allocator类  
        1. new 将内存分配和对象构造连在一起，allocator定义在memory中，它将内存分配和对象构造分离开来。
        2. 它分配的内存是原始的，未构造的

                allocator<string> alloc;               //可以分配string的allocator对象
                auto const p = alloc.allocate(n)       //分配n个未初始化的string
                这个allocate调用为n个string分配了内存

        3. 操作
            1. allocator<T> a
            2. a.allocate(n)
            3. a.deallocate(p, n)    调用之前，用户必须对每个在这块内存中创建的对象调用destroy
            4. a.construct(p, args)  args被传递给类型为T的构造函数
            5. a.destroy(p)

        4. allocator分配未构造的内存
            1. 为了使用allocate返回的内存，我们必须使用construct构造对象，使用未构造的内存，其行为未定义。
            2. construct接受一个指针和零个或多个的额外参数，额外参数传递个类型对象的构造函数做参数
                    
                    allocator<string> alloc;
                    auto const p = alloc.allocate(n)

                    auto q = p;
                    alloc.construct(q++);
                    alloc.construct(q++, 10, 'c');
                    alloc.construct(q++, "hi");

        5. 使用完对象后，必须对每个构造元素调用destroy来销它们
            
                while (q != p):
                    alloc.destroy(--q);

            1. 我们只能对真正构造了的元素进行destroy操作。
            2. 一旦元素被销毁，就可以重新使用这部分内存来保存其他的string，也可以将其归还给系统，释放内存通过调用deallocate来完成。

                    alloc.deallocate(p, n);

                    p必须是allocate分配的内存指针，n必须与分配的内存大小一致。

        6. 拷贝和填充未初始化内存的算法
            1. memory文件中定义了两个伴随算法，可以在未初始化的内存中创建对象。
            2. 算法
                1. uninitialized_copy(b, e, b2)
                2. uninitialized_copy_n(b, n, b2)
                3. uninitialized_fill(b, e, t)
                4. uninitialized_fill_n(b, n, t)

3. 使用标准库

## 容器
### 顺序容器
1. 概述
    1. 容器类型：
        1. vector: 可变大小数组
        2. deque: 双端队列
        3. list: 双向链表
        4. forward_list: 单向链表
        5. array: 固定大小数组，支持快速访问，不能添加或删除元素
        6. string: 插入/删除速度快

2. 容器库概览: 所有容器都适用的操作
    0. 操作
        1. iterator
        2. const_iterator
        3. size_type
        
        4. difference_type
        5. value_type
        6. reference
        7. const_reference
        
        8. 构造函数
            1. C c;       默认构造函数
            2. C c1(c2);  拷贝赋值
            3. C c(b, e); 迭代器范围拷贝：array 不支持
            4. C c{a, b, c...}; 列表初始化

        9. 赋值与swap
            1. c1 = c2
            2. c1 = {a, b, c...}
            3. a.swap(b)    交换a和b的元素
            4. swap(a, b)

        10. 大小
            1. c.size() : 元素数目，不支持forward_list
            2. c.max_size() ：可保存的最大元素数目
            3. c.empty()

        11. 添加/删除元素：不适用于array
            1. c.insert(args)
            2. c.emplace(inits)
            3. c.erase(args)
            4. c.clear()

        12. 关系运算符
            1. ==, !=
            2. <, <=, >, >=

        13. 获取迭代器
            1. c.begin(), c.end()
            2. c.cbegin(), c.cend()

        14. 反向容器的额外成员(不支持forward_list)
            1. reverse_iterator
            2. const_reverse_iterator
            3. c.rbegin(), c.rend()
            4. c.crbegin(), c.crend()

    1. 迭代器
        1. 插入迭代器
            1. back_iterator
            2. front_iterator
            3. iterator iterator：注意，iterator在声明时就绑定了iterator的位置，这个位置只初始化一次，以后每次插入时，新元素还是插入到初始化时指定元素的前面。

                    *it = val;
                    相当于
                    it = c.insert(it, val);
                    it++

                    一定要注意，it的位置移动了，它还是指向声明时指定的位置。这个位置不停变换。

        2. 流迭代器
            > iostream 类型不是容器，但是标准库定义了可以使用这些IO类型对象的迭代器

            1. istream_iterator
            2. ostream_iteraor
        3. 反向迭代器
        4. 移动迭代器

    2. 容器类型成员
    3. begin 和 end 成员
    4. 容器定义和初始化
        1. 除了拥有的4个初始化方法以外还有：
            1. C c1 = c2
            2. C c = {a, b, c...}
            3. 只有顺序容器(不包括array)的构造函数才能接受大小参数
                1. C seq(n)  String不适用。
                2. C seq(n, t)
        2. 标准库array具有固定大小
        
    5. 赋值和swap
        1. 赋值运算
            1. c1 = c2
            2. c = {a, b, c...}
            3. swap(c1, c2)
            4. c1.swap(c2)
            5. assign 不使用与关联容器和array
                1. seq.assign(b, e)
                2. seq.assign(il)
                3. seq.assign(n, t)

        2. 赋值运算会导致指向左边容器内部的迭代器、引用和指针失效，而swap操作将容器内容交换不会导致指向容器的迭代器、引用和指针失效，array和string除外。

    6. 容器大小操作
    7. 关系运算符

3. 顺序容器操作
    1. 向顺序容器添加元素
        1. 顺序容器(非array)添加元素的操作
            1. 这些操作会改变容器大小，所以array不支持这些操作
            2. forward_list 有自己版本的insert和emplace，forward_list不支持push_back和emplace_back，vector和string不支持push_front和emplace_front
            3. c.push_back(t)， c.emplace_back(args), 返回void
            4. c.push_font(t),  c.emplace_front(args)
            5. c.insert(p, t),  c.emplace(p, args)  返回指向新添加的元素的迭代器
            6. c.insert(p, n, t)   返回指向新添加的第一个元素的迭代器，若n为0，则返回p
            7. c.insert(p, b, e)
            9. c.insert(p, il)

    2. 访问元素
        1. at 和下标操作只适用于string、vector、deque和array。因为这些是固定空间的容器，根据下标可以很容易计算得到实际的内存地址，而链表不可以计算得到，只能遍历。back不适用于forward_list，因为单向链表如果想要访问最后一个元素是O(n)，他需要遍历整个单向列表。forward_list不支持所有的访问最后一个元素的操作。
        2. c.back()
        4. c.font()
        5. c[n]
        6. c.at[n]  ：这几个函数返回的都是引用

    3. 删除元素
        1. 这些操作会改变容器的大小，所以不适用于array
        2. forward_list有特殊版本的erase，不支持pop_back; vector和string不支持pop_front。
        3. c.pop_back()
        4. c.pop_front()
        5. c.earse(p)
        6. c.earse(b, e)
        7. c.clear()

    4. 特殊的forward_list 操作
        1. 单向列表，只有前一个元素才可以知道现在这个元素的指针，更改某个元素，需要改变它的前端元素的指针。
        2. lst.before_begin(), 这个迭代器不能解引用
        3. lst.cbefore_begin()
        4. 插入：
            1. lst.insert_after(p, t)
            2. lst.insert_after(p, n, t)
            3. ls.insert_after(p, b, e)
            4. ls.insert_after(p, il)   il是一个花括号列表，返回一个指向最后一个插入元素的迭代器
            5. emplace_after(p, args)
        5. 删除
            1. lst.erase_after(p)
            2. lst.erase_after(b, e)

    5. 改变容器大小
        1. resize可以用来增大或缩小容器，array不支持
        2. 如果当前大小大于所要求的的大小，容器后面的元素会被删除
        3. 如果当前大小小于新大小，会将新元素添加到容器后部
        4. c.resize(n) :没有提供第二个参数，进行值初始化，如果是类，必须有构造函数。
        5. c.resize(n, t)

    6. 容器操作可能使迭代器失效
        1. 不要保存end返回的迭代器

    7. 总结：
        1. array是固定长度，所有改变长度的方法都不能使用。pop、push_back、insert
        2. vector、string是块内存空间的，如果改变最开始的元素，需要移动所有后面的元素，所以不支持操作front元素：不支持  push_front、emplace_front、pop_font、
        3. forw_list是单向列表，如果访问最后的元素需要遍历整个链表，所以应该不支持末尾的操作：push_back、emplace_back、back。

4. vector对象是如何增长的
    1. vector和string通常会分配比新的空间需求更大的内存空间，容器预留这些空间作为备用，可用来保存更多的新元素。
    2. 函数
        1. c.shrink_to_fit() :只能用于vector、string、deque。请将capacity()减少为与size()相同大小。
        2. c.capacity() ：不重新分配内存空间的话，c可以保存多少元素
        3. c.reserve(n): 分配至少能容纳n个元素的内存空间
    3. capacity和size
        1. capacity是最多能容纳多少个元素，size是已经保存的元素的数目。

5. 额外的string 操作
    1. 构造string的其他方法
        1. string s(cp, n) :s是cp指向的数组的前n个字符的拷贝，次数组至少应该包含n个字符
        2. string s(s2, pos2): 
        3. string s(s2, pos2, len2):s是string s2 从下标pos2开始的len2个字符的拷贝。若pos2>s2.size(),构造函数未定义。
        4. substr：返回一个string，它是原始string的一部分或全部的拷贝。
            1. string s("Hello world"); string s2 = s.substr(0, 5); string s3 = s.substr(6);

    2. 改变string的其他方法
    3. string 搜索操作
    4. compare函数
    5. 数值转换

6. 适配器
    > 容器、迭代器和函数都有适配器。适配器是一种机制，能使某种事物的行为看起来像是另外意纵横事物一样。
    > 一个容器适配器接受一种已有的容器类型，使其行为看起来像是一种不同的类型。

    1. 顺序容器适配器
        1. stack
            1. 定义在 stack头文件
            2. stack基于deque实现，也可以在list或vector上实现
            3. 操作
                1. s.pop()
                2. s.push()
                3. s.emplace(args)
                4. s.top()

        2. queue, priority_queue
            1. 定义在queue头文件
            2. queue默认基于deque实现，priority_queue默认基于vector是吸纳
            3. 操作
                1. q.pop()  ：返回首元素或最高优先级的元素
                2. q.front()：返回首或尾但不删除
                3. q.back() :只适用于queue
                4. q.top() :
                5. q.push(item)
                6. q.emplace(args)

    2. 所有容器适配器都支持的操作和类型
        1. size_type
        2. value_type
        3. container_type
        4. A a;
        5. A a(c)
        6. 关系运算符
        7. a.empty()
        8. a.size()
        9. swap(a, b)
        10. a.swap(b)

### 关联容器
1. 类型
    1. map
    2. set
    3. multimap
    4. multiset
    5. unorderd_map
    6. unorderd_set
    7. unorderd_multimap
    8. unorderd_multiset

2. 概述
    1. 关键字类型
        1. 有序容器的关键字类型
            1. 我们可以提供自定义的的操作来代替关键字上的 < 运算符
            2. 所提供的关键字类型上定义一个严格弱序。
            3. 如果一个类型定义了“行为正常”的 < 运算符，则它可以用作关键字类型。

        2. 使用关键字类型的比较函数

    2. pair类型
        > 头文件 utility
        > pair的默认构造函数对数据成员进行值初始化

        1. 操作
            1. pair<T1, T2> p;
            2. pair<T1, T2> p(v1, v2)
            3. pair<T1, T2> p = {v1, v2};
            4. make_pair(v1, v2);
            5. p.first
            6. p.second
            7. p1 relop p2
            8. p1 == p2
            9. p1 != p2
3. 操作
    1. 关联容器额外的类型别名
        1. key_type
        2. mapped_type
        3. value_type

    2. 关联容器迭代器
        1. 当解引用一个关联容器时，我们会得到一个类型为容器的value_type的值的引用
        2. 对map而言，value_type是一个pair类型，其first成员保存const的关键字，second成员保存值
        3. set的迭代器是const的
        4. 遍历关联容器
            
    3. 添加元素
        1. map的insert，元素类型必须是pair
            1. word_count.insert({word, 1});
            2. word_count.insert(make_pair(word, 1));
            3. word_count.insert(pair<string, size_t>(word, 1));
            4. word_count.insert(map<string, size_t>::value_type(word, 1));

        2. 关联容器insert操作
            1. c.insert(v)
            2. c.emplace(args)
            3. c.insert(b, e)
            4. c.insert(il)
            5. c.insert(p, v)
            6. c.emplace(p, args)

        3. 总结
            1. 对于map和set，只有当元素的关键字不在c中时才插入(或构造)元素，返回一个pair，包含一个迭代器，指向关键字元素，以及一个指示插入是否成功的bool的值。

        4. 检测insert的返回值
            1. map、set返回一个pair
            2. multimap、multiset返回一个迭代器

    4. 删除元素
        1. 操作
            1. c.erase(k)  返回size_type值
            2. c.erase(p)  返回p之后的迭代器
            3. c.erase(b, e)  返回e

    5. map下标操作：只有map和unorder_map两种容器有
        1. 如果关键字不在map中，会为他创建一个元素并插入到map中，关联值将进行值初始化。
        2. 因为下标运算可能插入一个新元素，我们只可以对非const的map使用下标操作。
        3. c[k]     返回关键字为k的元素
        4. c.at[k]  带参数检查，如果k不在c中，抛出out_of_range异常

        5. 下标操作的返回值
            1. 通常，解引用一个迭代器所返回的类型与下标运算符返回的类型是一样的，但对map则不然
            2. 对map进行下标操作时，会获得一个mapped_type对象，但当解引用一个map迭代器时，会返回一个value_type对象。
            3. **与其他下标运算符相同的是，map的下标操作返回一个左值，所以既可以读，也可以写。返回的应该是对象的空间地址，像引用的类型一样应该**
   
    6. 访问元素
        1. 下标和at操作只适用于非const的map和unordered_map
        2. lower_bound和upper_bound 不适用于无序容器。
        3. 操作
            1. c.find(k)
            2. c.count(k)
            3. c.lower_bound(k)
            4. c.upper_bound(k)
            5. c.equal_range(k)  : 返回一个pair，表示关键字等于k的元素范围，若k不存在，pair的两个成员均等于c.end()

4. 无序容器
    1. 哈希函数映射到不同的存储空间
    2. 操作
        1. 桶接口
            1. c.bucket_count()
            2. c.max_bucket_count()
            3. c.bucket_size(n)
            4. c.bucket(k)
        2. 桶迭代
            1. local_iterator
            2. const_local_iterator
            3. c.begin(n), c.end(n)
            4. c.cbegin(n), c.cend(n)
        3. 哈希策略
            1. c.load_factor()
            2. c.max_load_factor()
            3. c.rehash(n)
            4. c.reserve(n)

    3. 无序容器对关键字类型的要求
        1. 默认无序容器使用关键字类型的 == 运算符来比较元素，它们还是用一个hash<key_type> 类型的对象来生成每个元素的哈希值
        2. 内置类型(包括指针)标准库提供了hash模板，string和智能指针定义了hash，一次可以直接定义关键字是内置类型(包括指针类型)、string、智能指针的无序容器
        3. 不能直接定义关键字为自定义类型的无序容器，不能直接使用hash模板，必须提供我们自己的hash模板版本。

## 面向对象

### 类

### 面向对象设计

1. 面向对象设计基于三个基本概念：
    1. 数据抽象：可以将类的接口与实现分离
    2. 继承:  可以定义相似的类型并对其相似关系建模
    3. 动态绑定：可以在一定程度上忽略相似类型的区别

2. 继承
    1. 虚函数
        > 对于某些函数，基类希望它的派生类个自定义适合自己的版本，此时基类就将这些函数声明成为虚函数

        1. 派生类必须在其内部对所有重新定义的虚函数进行声明。

    2. 派生类使用类派生列表明确指定它是从哪个(哪些)基类继承而来。格式，先是一个冒号，后面紧跟逗号分隔的基类列表，每个基类前可以有访问说明符

    3. 动态绑定
        > 在C++语言中，当我们使用基类的引用(或指针)调用一个虚函数时，将发生动态绑定。

3. 定义基类和派生类
    1. 基类通常都应该定义一个虚析构函数，即使该函数不执行任何实际操作也是如此。
    
    2. 定义基类
        1. 基类两种成员函数
            1. 希望派生类覆盖的函数，将其定义为虚函数。当使用指针或引用调用虚函数时，该调用将被动态绑定，根据引用或指针所绑定对象的类型不同，该调用执行不同的派生或者基类版本。
            2. 希望派生类继承不要改变的函数。

        2. virtual只能出现在类内部的生命语句之前。
        3. **成员函数如果没被声明为虚函数，则其解析过程发生在编译时而非运行时。**
        4. protected，派生类可以访问，其他对象不可访问，private对象，派生类也不可以访问。

    4. 定义派生类
        1. 派生类必须通过类派生列表指定基类
        2. 每个基类前可以有一下三种说明符：public、protected、private
        3. 派生类必须将继承来的成员函数中需要覆盖的那些重新声明。这些函数前面可以加上virtual，但不强制这样做。C++11允许在重写的成员函数后添加override关键字
        4. 内存中，派生类的内存空间是：前面是基类的对象空间，后面是派生类新定义的对象。但内存不一定是连续的存储的。
        5. 因为在派生类对象中含有基类对应的部分，所以我们可以把派生类当成基类对象来使用。而且也可以把基类的指针或引用绑定到派生类对象中的基类部分上。
        6. 派生类构造函数
            1. 派生类不能直接初始化基类的成员，必须使用基类的构造函数来初始化它的基类部分。
            2. 每个类控制它自己的成员的初始化过程。
            3. 派生类同样是通过构造函数初始化列表来将实参传递给基类构造函数的：
                
                    quote(const std::string& book, double p, std::size_t qty, double disc):Base(book, p), min_qty(qty), discount(disc) {}

            4. 首先初始化基类的部分，然后按照声明的顺序依次初始化派生类的成员。
                1. 始化列表的顺序要跟你在类声明的顺序要一致，否则可能出现问题。
                2. 只要成员变量的初始化不依赖其他成员变量，即使顺序不同也能正确的初始化
                3. **成员变量在使用初始化列表初始化时，与构造函数中初始化成员列表的顺序无关，只与定义成员变量的顺序有关**
    5. 静态成员
        1. 如果基类定义了一个静态成员，则在整个继承体系中只存在该成员的唯一定义。无论派生多少类，每个静态成员都只存在唯一的实例。 

    6. 派生类的声明
        1. 派生类的声明包含类型但是不包含它的派生列表：
            
                class quote: base;   //错误，派生列表不能出现在这里
                class quote;         //正确

    7. 派生类的基类必须是已经定义而非是仅仅声明的。一次，一个类不能派生它本身。

    8. 不能继承的类：在类名后跟一个关键字final。

    9. 类型转换
        1. 智能指针和内置指针一样都支持指向基类的指针可以绑定到派生类。
        2. 
## 内存空间
### 内存总结
### 类内存空间
1. 派生类的内存空间
    1. 类的非虚函数其实不占用类对象的内存（函数编译后形成二进制文件放在内存中的代码段区）
    2. 如果类含有虚函数，则编译结果 在类开始位置插入了一个虚函数指针。
    3. 内存中，派生类的内存空间是：前面是基类的对象空间，后面是派生类新定义的对象。但内存不一定是连续的存储的。
    4. 类所占内存的大小是由成员变量（静态变量除外）决定的，成员函数是不计算在内的。

2. 总结：
    1. 空的类是会占用内存空间的，而且大小是1，原因是C++要求每个实例在内存中都有独一无二的地址。

    2. 类内部的成员变量：
        1. 普通的变量：是要占用内存的，但是要注意对齐原则（这点和struct类型很相似）。
        2. static修饰的静态变量：不占用内容，原因是编译器将其放在全局变量区。

    3. 类内部的成员函数：
        1. 普通函数：不占用内存。
        2. 虚函数：要占用4个字节，用来指定虚函数的虚拟函数表的入口地址。所以一个类的虚函数所占用的地址是不变的，和虚函数的个数是没有关系的
