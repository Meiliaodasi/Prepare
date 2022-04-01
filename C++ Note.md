1. int sum_arr(int arr[], int n){
   
   }  
   int cookies[3] = {0,1,2};  
   数组名解释为第一个元素的地址： cookies = &cookies[0];  
   用于函数头或函数原型时 int *arr和int arr[]相同   
2. 
    ```C++
   class Autolock {
        public:
            //构造的时候调用lock。
            inline Autolock(Mutex& mutex) : mLock(mutex)  { mLock.lock(); }
            inline Autolock(Mutex* mutex) : mLock(*mutex) { mLock.lock(); }
            //析构的时候调用unlock。
            inline ~Autolock() { mLock.unlock(); }
        private:
            Mutex& mLock;
        };
    ```      
  ~折构函数  
 析构函数特点：  
    无参数且无返回值  
    一个类只能有且有一个析构函数，如果没有显式的定义，系统会生成一个缺省的析构函数（合成析构函数）  
    析构函数不能重载，每有一次构造函数的调用就会有一次析构函数的调用  
    
3. inline 内联函数  
   template 模板  
4. 引用变量
```
   int  years = 5; 
   int & my = years;
   int * we = &years;
```  
   my 和 *we 同 years;  
   &my 和 we 同 &years  
   返回引用时避免返回临时变量的引用或指针(如果函数返回是String，不是引用没有问题)，可以返回一个作为参数传递给函数的引用  
5. const 前指后值  
6. 结构体指针可以通过 -> 操作符 来访问结构体中的成员  
7. template --- 声明创建模板  
&emsp;typename --- 表面其后面的符号是一种数据类型，可以用class代替
&emsp;T --- 通用的数据类型，名称可以替换，通常为大写字母  
8. 指针类型定义  
例如： typedef int *pointer;  
描述： pointer等价于 int *定义;pointer a声明等价于int *a声明  
函数指针  
例如： typedef int (*func)(void)  
描述： func等价于int (*)(void)类型  
func pf等价于int (*pf)(void)声明，pf是一个函数指针变量  
9. fixed arrays decay into pointers when passed to a function, so we have to pass the length as a separate parameter.  
10. 指针作为方法参数，指针值传递，指向相同的值，修改实参指向不会改变原指针，修改实参指向的值会改变原指向。  
11. 
```C++
Here’s an example of a class that has a const member variable:
class Something
{
private:
    const int m_value;
 
public:
    Something(): m_value{ 5 } // directly initialize our const member variable
    {
    } 
};

This works because we’re allowed to initialize const variables (but not assign to them!).
```  
### 值传递
形参都是值传递。但是这个值如果是指针的话，是可以改变指针指向内容的值，即实参的值。这个要弄清两个概念：指针和指针指向的数据（地址和值）。  
形参的值是实参的地址，并不是实参的值，所以形参的值的改变只是指针的改变，即指向数据的地址改变，并不是指针指向数据的改变，实参的地址也不会被改变。  
[指针作为函数参数](https://blog.csdn.net/qq_33706673/article/details/84669784)  
[字符串常量](https://blog.csdn.net/coutamg/article/details/53453909)
