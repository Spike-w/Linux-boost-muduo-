# Linux-boost-muduo-

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
