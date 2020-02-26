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
   
