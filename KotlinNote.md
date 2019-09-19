1.Kotlin不区分导入的是类还是函数，它允许使用import关键字导入任何种类的声明。可以直接导入顶层函数的名称。  
2.扩展函数  
  ```
   package strings
   fun String.lastChar():char = get(length-1)
  ```
