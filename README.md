# Linux下安装boost（muduo库安装前置）

Boost开发环境搭建 
Linux下 Boost库环境搭建 
先把Linux系统下的boost源码包boost_1_69_0.tar.gz（附件中）拷贝到某一指定路径下，然后解压，如下：
root@ubuntu:/home/joecoder/Packages# ls 
boost_1_69_0.tar.gz  
运行命令：tar -zxvf boost_1_69_0.tar.gz   
tar解压完成后，进入源码文件目录，查看内容 
root@ubuntu:/home/joecoder/Packages# cd boost_1_69_0 
root@ubuntu:/home/joecoder/Packages/boost_1_69_0# ls b2      boost-build.jam  bootstrap.bat  index.htm   libs                rst.css bin.v2  boostcpp.jam     bootstrap.log  index.html  LICENSE_1_0.txt     stage bjam    boost.css        bootstrap.sh   INSTALL     more                status boost   boost.png        doc            Jamroot     project-config.jam  tools  
运行bootstrap.sh工程编译构建程序，需要等待一会儿，查看目录： 
Building Boost.Build engine with toolset gcc... tools/build/src/engine/bin.linuxx86_64/b2 Unicode/ICU support for Boost.Regex?... not found. Generating Boost.Build configuration in project-config.jam...  
Bootstrapping is done. To build, run:      ./b2      
To adjust configuration, edit 'project-config.jam'. Further information:     
- Command line help:      ./b2 --help
- - Getting started guide:       http://www.boost.org/more/getting_started/unix-variants.html          - Boost.Build documentation:      http://www.boost.org/build/doc/html/index.html
源码根目录下生成了b2程序，运行b2程序如下（boost源码比较大，这里编译需要花费一些时间）：
【注意】：如果Linux系统没有安装g++编译器，需要先安装g++，建议g++4.6版本以上，能比较好的支持C++新标准，可以通过命令 g++ --version 查看g++版本号 root@ubuntu:/home/joecoder/Packages/boost_1_69_0# ./b2
编译完成后，会有如下打印:
The Boost C++ Libraries were successfully built!
最后，再把上面的boost库头文件和lib库文件安装在默认的Linux系统头文件和库文件的搜索路径下，运行下面命令（因为要给/usr目录下拷贝文件，需要先进入root用户）：
【注意】：一定是在root模式下：
root@ubuntu:/home/joecoder/Packages/boost_1_69_0# ./b2 install
安装完成后，最后会有如下信息打印出来：
ln-UNIX /usr/local/lib/libboost_wave.so common.copy /usr/local/lib/libboost_exception.a common.copy /usr/local/lib/libboost_system.a common.copy /usr/local/lib/libboost_chrono.a common.copy /usr/local/lib/libboost_timer.a common.copy /usr/local/lib/libboost_test_exec_monitor.a ...updated 14831 targets...
验证安装boost是否成功，通过下面的代码验证一下：
#include <iostream>
#include <boost/bind.hpp>
#include <string>
using namespace std;
class Hello {
public:
void say(string name)  	{
cout << name << " say: hello world!" << endl;
} };
int main() {
Hello h;
auto func = boost::bind(&Hello::say, &h, "zhang san");
func();
return 0;
} 通过g++编译上面的代码，运行打印如下：
zhang san say: hello world!
至此，Linux下的boost库安装成功！
![image](https://github.com/user-attachments/assets/8d3e251c-b992-4d1b-8cef-42fa7352f0f9)


# Linux下安装muduo库
关于最后成功安装muduo库报错：
![image](https://github.com/user-attachments/assets/60201b02-a69d-499a-a767-a3c9b2360bb9)

解决办法：
在编译 muduo 库时，Boost 库头文件里存在一些编译警告，并且这些警告被当作错误处理，从而致使编译失败。下面为你分析具体问题并给出解决办法：
问题分析

类型转换警告：/usr/include/boost_1_69_0/boost/type_traits/is_complete.hpp:47:14 处的错误提示，表明从 long unsigned int 转换为 unsigned int 时可能会改变值，这是一个潜在的精度丢失问题。
旧风格强制类型转换警告：在多个 Boost 头文件里都有 use of old-style cast 这样的警告，这是因为使用了旧风格的强制类型转换（像 (Model*)0 这种形式），而现代 C++ 更推荐使用 static_cast、dynamic_cast、const_cast 和 reinterpret_cast。
警告被当作错误处理：cc1plus: all warnings being treated as errors 显示编译器把所有警告都当作错误处理，只要有警告就会让编译失败。
解决办法
1. 忽略特定警告

可以在编译选项里添加 -Wno-conversion 和 -Wno-old-style-cast 来忽略类型转换和旧风格强制类型转换的警告。

在 muduo 项目的 CMakeLists.txt 文件里，找到编译选项相关的部分，添加如下内容：

收起
cmake
#设置编译选项，忽略类型转换和旧风格强制类型转换警告
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-conversion -Wno-old-style-cast")

添加完成后，重新进行编译：

收起
bash
#进入 muduo 项目的构建目录（如果没有则创建）
cd /home/joecoder/Packages/muduo-master
mkdir build
cd build
#重新运行 CMake 配置
cmake ..
#编译项目
make
2. 升级 Boost 库

这些警告可能是由于 Boost 库版本过旧导致的。你可以尝试升级 Boost 库到最新的稳定版本，然后重新编译 muduo 库。

最后成功安装，如下：
![image](https://github.com/user-attachments/assets/1425cc0e-fa95-41ba-880d-419367547c47)
