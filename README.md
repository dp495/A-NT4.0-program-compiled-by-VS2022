# A-NT4.0-program-compiled-by-VS2022
使用VS2022编译适用于早期Windows系统的二进制文件的例子与教程

## 教程部分
### 一、在VS2022中添加对Win XP的支持

#### 1.为VS添加组件
在开始菜单中找到Microsoft Visual Studio Installer并打开   
![开始菜单中的Visual Studio](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/open%20vs%20installer.png)

点击“修改”，在弹出窗口中选择“单个组件”，搜索"v141"，勾选图示的组件并安装   
![点击“修改”](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/add%20packs_1.png)  
![需要的条目](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/add%20packs_2.png)  
   

#### 2.为项目添加支持WinXP的配置   
安装完成后打开要编译的项目，在顶端菜单中选择“生成”->配置管理器，按图示操作  
![配置管理器](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/settings_1.png)  
名称不要太长且最好具有辨识度  
![新建](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/settings_2.png)  
切换到创建的配置并把架构改为x86(顶端菜单下的一排中的两个下拉菜单)  
   

#### 3.修改该配置   
在旁侧的解决方案资源管理器在要更改的项目上右键 -> 属性  
![属性](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/settings_3.png)
- 配置属性 -> 常规 -> 平台工具集 -> Visual Studio 2017 - Window XP (v141_xp)   
- 配置属性 -> 高级 -> MFC的使用 -> 在静态库中使用MFC  
- C/C++ -> 语言 -> 符合模式 -> 否  
   
   
此时应该可以正常编译大多数项目了（建议在这里测试成功再继续）  
如果对更早的兼容性没有要求就可以在这里停下了   
更多的兼容性异常解决方法请看[这里](https://blog.csdn.net/chy555chy/article/details/123399974)  

### 二、添加对早期系统的支持(最早到WinNT4.0/Win98)

#### 1.添加masm   
在更改的项目上右键 -> 生成依赖项 -> 自定义   
![生成依赖项](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/settings_4.png)   
在弹出窗口中勾选masm   
![勾选nasm](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/settings_5.png)   
   

#### 2.添加补充早期项目中缺失的API的代码   
在更改的项目的源文件文件夹上右键 -> 添加 -> 现有项   
选择在/src中下载的extapi.cpp与impapi.asm(建议先把它们放入工作文件夹中)   
(注：这两个文件实现了对Win XP新增api的补充，来自[一个stackoverflow的回答](https://stackoverflow.com/questions/19516796/visual-studio-2012-win32-project-targeting-windows-2000)，若有版权/开源协议问题请及时在Issues里指出)   
   

#### 3.修改项目配置   
在要更改的项目上右键 -> 属性   
- 链接器 -> 高级 -> 映像具有安全异常处理程序 -> 否  
 
可选内容(减小可执行文件大小):   
- 链接器 -> 清单文件 -> 生成清单 -> 否 (如果有uac需要则“生成清单必须为“是”)   
- 链接器 -> 调试 -> 生成调试信息 -> 否   
- 链接器 -> 高级 -> 随机基址 -> 否   

保存此配置并生成一个.exe文件   
   

#### 4.更改PE文件头中的版本信息   
微软不允许链接器 -> 系统 -> 所需的最低版本 低于5.01(详见[这里](https://docs.microsoft.com/en-us/cpp/build/reference/subsystem-specify-subsystem?view=msvc-170))   
所以需要手动编辑.exe文件的PE文件头   

使用任意二进制编辑器打开生成的.exe文件   
找到ASCII"PE"对应的 0x50 0x45(每个文件不同)   
![寻找版本信息](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/edit_1.png)   
在这个0x50对应的地址上加0x40就是版本信息所在的地址   
将两个0x50 0x00 0x01 0x00改为 0x40 0x00 0x00 0x00   
![更改版本信息](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/edit_2.png)   
保存即可   
   

### 三、运行效果   

- NT4.0   
![例1](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/results_1.png)   
- Win XP   
![例2](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/results_2.png)    
- Win 10 x64   
![例3](https://github.com/dp495/A-NT4.0-program-compiled-by-VS2022/blob/main/Pictures/results_3.png)   
   

### 四、已知问题

1. 链接器报error LNK2001: 无法解析的外部符号   
解决方法：在MSDN上查询该函数需要的静态库(以User32.lib为例)   
并在文件最前方添加以下代码再编译即可   
~~~c
#pragma comment (lib,"User32.lib")
~~~
      

2. 上文添加的代码补充了大多数常用API(包括Unicode)，但是并不是全部   
有些API也没必要在老系统上实现(如多线程相关API)   
遇到运行时找不到接入点的问题请在Pull requsets上提出并附上截图   
如果能顺手贴上解决的代码就更好了(笑)  
   
**如果这对你有用，不妨点个Star**
