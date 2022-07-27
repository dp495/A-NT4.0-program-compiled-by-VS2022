# A-NT4.0-program-compiled-by-VS2022
使用VS2022编译最早支持NT4.0的二进制文件的例子与教程

## 教程部分
### 一、在VS2022中添加对Win XP的支持

在开始菜单中找到Microsoft Visual Studio Installer并打开   
![开始菜单中的Visual Studio](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/open%20vs%20installer.png)

点击“修改”，在弹出窗口中选择“单个组件”，搜索"v141"，勾选图示的组件并安装   
![点击“修改”](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/add%20packs_1.png)  
![需要的条目](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/add%20packs_2.png)  

安装完成后打开要编译的项目，在顶端菜单中选择“生成”->配置管理器，按图示操作  
![配置管理器](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/settings_1.png)  
名称不要太长且最好具有辨识度  
![新建](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/settings_2.png)  
切换到创建的配置并把架构改为x86(顶端菜单下的一排中的两个下拉菜单)  

在旁侧的解决方案资源管理器在要更改的项目上右键 -> 属性  
![属性](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/settings_3.png)
配置属性 -> 常规 -> 平台工具集 -> Visual Studio 2017 - Window XP (v141_xp)   
配置属性 -> 高级 -> MFC的使用 -> 在静态库中使用MFC  
C/C++ -> 语言 -> 符合模式 -> 否  

此时应该可以正常编译大多数项目了（建议在这里测试成功再继续）  
如果对更早的兼容性没有要求就可以在这里停下了   
更多的兼容性异常解决方法请看[这里](https://blog.csdn.net/chy555chy/article/details/123399974)  

### 二、添加对早期系统的支持(最早到WinNT4.0/Win98)

在更改的项目上右键 -> 生成依赖项 -> 自定义   
![生成依赖项](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/settings_4.png)   
在弹出窗口中勾选masm   
![勾选nasm](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/settings_5.png)   
在更改的项目的源文件文件夹上右键 -> 添加 -> 现有项   
选择在/src中下载的extapi.cpp与impapi.asm(建议先把它们放入工作文件夹中)   
(注：这两个文件实现了对Win XP新增api的补充，来自[一个stackoverflow的回答](https://stackoverflow.com/questions/19516796/visual-studio-2012-win32-project-targeting-windows-2000)，若有版权/开源协议问题请及时联系我)   
在要更改的项目上右键 -> 属性   
链接器 -> 高级 -> 映像具有安全异常处理程序 -> 否   



