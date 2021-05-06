1.在framework/base/core/res/res 下添加资源文件后需要先编译资源 然后编译framework 才可正常引用

进入项目根目录 cd frameworks/base/core/res/ 执行mm命令, 编译 framework-res.apk

或 ./mk mm frameworks/base/core/res

编译完后com.android.internal.R中会生成资源的引用。 

2.在目录frameworks/base/ 下执行mm 编译 framework.jar  

或 ./mk mm frameworks/base 

或 make framework

生成classes.jar  Y:\Di2L\out\target\common\obj\JAVA_LIBRARIES\framework_intermediates\classes.jar 

3.如果 frameworks/base/services 下有修改，则也要编译

frameworks/base/services/java/ 执行mm 编译  services.jar

或./mk mm frameworks/base/services/java   

4.adb remount

  adb push framework-res.apk /system/framework/

  adb push framework.jar /system/framework/

  adb push services.jar /system/framework/   
**常见问题**  
没有classes.jar  make update-api

烧写 adb reboot loader    
fastboot devices  
	 fastboot flash system Y:\Di2L\out\target\product\msm8953_64\system.img  
