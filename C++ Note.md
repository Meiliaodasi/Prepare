1. int sum_arr(int arr[], int n){
   
   }  
   int cookies[3] = {0,1,2};  
   数组名解释为第一个元素的地址： cookies = &cookies[0];  
   用于函数头或函数原型时 int *arr和int arr[]相同   
2. 
    ```
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
