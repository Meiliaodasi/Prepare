#### 源码Build  
1.source build/envsetup.sh  
2.lunch （lunch 5）  
3.make （make -j6） 
#### Android Studio导入系统源码
1.查看out/host/linux-x86/framework/idegen.jar是否存在，如果不存在，进入源码根目录执行如下的命令:  
source build/envsetup.sh  
lunch [选择整编时选择的参数或者数字]  
mmm development/tools/idegen/  
2.在out/host/linux-x86/framework目录下生成idegen.jar，执行下面生成android.iml 和 android.ipr    
sudo development/tools/idegen/idegen.sh  

sudo chmod 777 android.iml  
sudo chmod 777 android.ipr  

问题解决：  
1.sudo apt-get install m4  



[ 98% 100457/101515] //frameworks/base/services/appwidget:services.appwidget javac [common]03:46:23 ninja failed with: signal: killed

问题：  
#### 1.failed to build some targets (03:09:03 (hh:mm:ss)) （增加内存）
2.ninja: error: 'out/target/common/obj/JAVA_LIBRARIES/SettingsLib_intermediates/package-res.apk', needed by 'out/target/product/generic/obj/APPS/SystemUI_intermediates/package-res.apk', missing and no known rule to make it
23:22:00 ninja failed with: exit status 1 （首次单独编译mk出错等问题）  
执行mma，再不行mmma  




