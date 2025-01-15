#  C++网络编程，从0实现Reactor高并发服务器

Linux 版本： Centos Stream9

## GCC安装

```cpp
#安装C和C++的编译器
yum -y install gcc*

#安装 GCC 工具链
dnf install -y gcc-toolset-12
#将gcc-toolset-12配置为默认环境
source /opt/rh/gcc-toolset-12/enable
source ~/.bashrc
#查看当前 GCC 版本
gcc --version

#安装库函数的帮助文档
yum -y install man-pages

/*帮助文档的使用*/
man级别命令或函数
显示帮助的界面可以用vi的命令，q退出。
man 的级别
1-系统命令；2-系统接口；3-库函数；4-特殊文件，比如设备文件；5-文件；
6-游戏；7-系统的软件包；8-系统管理命令；9-内核。
```

## 使用编译

gcc/g++选项 源代码文件1源代码文件2 源代码文件n

常用选项：

-o  指定输出的文件名，这个名称不能和源文件同名。如果不给出这个选项，则生成可执行文件a.out。  

```cpp
g++ -o [可执行文件名] [多个源文件]
```

-g 如果想对源代码进行调试，必须加入这个选项。

-On 在编译、链接过程中进行优化处理，生成的可执行程序效率更高。

-c  只编译，不链接成为可执行文件，通常用于把源文件编译成静态库或动态库。

-std=c++11 支持C++11标准。

优化选项：

-O0：不做任何优化，这是默认的编译选项。

-O 或-O1：对程序做部分编译优化，对于大函数，优化编译占用稍微多的时间和相当大的内存。使用本项优化，编译器会尝试减小生成代码的尺寸，以及缩短执行时间，但并不执行需要占用大量编译时间的优化。

-O2:这是推荐的优化等级。与O1比较而言，02优化增加了编译时间的基础上，提高了生成代码的执行效率。

-O3：这是最高最危险的优化等级。用这个选项会延长编译代码的时间，并且在使用gcc4.×的系统里不应全局启用。自从3.x版本以来gcc的行为已经有了极大地改变。在3.x，，-O3生成的代码也只是比-O2快一点点而已，而 gcc4.x中还未必更快。用-O3来编译所有的软件包将产生更大体积更耗内存的二进制文件，大大增加编译失败的机会或不可预知的程序行为（包括错误）。这样做将得不偿失，记住过犹不及。在 gcc 4.x.中使用-O3 是不推荐的。

如果使用了优化选项：1）编译的时间将更长；2）目标程序不可调试；3）有效果，但是不可能显著提升程序的性能。



## VSCode 连接 Linux

![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1735451701522-061c47a7-fdee-48fd-ba65-0bba9835cee5.png)

![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1735521914948-f1d6bd80-a39b-430f-b8f8-648cb1132e28.png)

![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1735521185530-9080b96f-0e28-436d-bd0a-8987ef9564dd.png)

![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1735452025221-84e9023f-78ad-41da-8ff1-928acaa7bd70.png)

安装和配置其它的C/C++插件。

![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1735525778207-fbc1c9af-1c42-4540-bfb7-f0b80cb87519.png)

![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1735525758947-f12095db-4db9-4d19-bc4a-ee5f0115d249.png)

```cpp
1.安装 Windows 可选功能 OpenSSH Client
或者手动下载 OpenSSH GitHub地址：https://github.com/PowerShell/Win32-OpenSSH/releases
配置 Windows 的环境变量
cmd: 
powershell.exe -ExecutionPolicy Bypass -File install-sshd.ps1
sc config sshd start=auto
net start sshd
测试: ssh ip

2.
cmd:
C:\Users\梦小七\.ssh:  ssh-keygen -t rsa -b 4096
C:\Users\梦小七\.ssh:  scp id_rsa.pub root@192.168.100.100:.ssh/.
Linux: 
cd .ssh
cat id_rsa.pub >> authorized_keys
```

## `Makefile`

- 用于自动化构建和管理项目的文件，通常在使用 `make` 工具时使用  

```cpp
[root@mengqi mengxiaoqi]# vim book.cpp 
#include <iostream>
using namespace std;
int main()
{
    cout << "Hello World\n";
    return 0;
}

[root@mengqi mengxiaoqi]# vim makefile 
all:book
book:book.cpp
        g++ -o book book.cpp
clean:
        rm book

[root@mengqi mengxiaoqi]# ls
book.cpp  makefile

[root@mengqi mengxiaoqi]# make
g++ -o book book.cpp
    
[root@mengqi mengxiaoqi]# ls
book  book.cpp  makefile

[root@mengqi mengxiaoqi]# ./book 
Hello World
```

编写规则文件 makefile

```cpp
[root@mengqi tools]# cat makefile 
# 指定编译的目标文件是libpublic.a和libpublic.so
all:libpublic.a \
    libpublic.so

# 编译libpublic.a需要依赖public.h和public.cpp
# 如果被依赖文件内容发生了变化,将重新编译libpublic.a
libpublic.a:public.h public.cpp
	g++ -c public.cpp -o public.o
	ar rcs libpublic.a public.o

libpublic.so:public.h public.cpp
	g++ -fPIC -shared -o libpublic.so public.cpp

# 清理编译目标文件,仅在 make clean 才会执行
clean:
	rm -f libpublic.a libpublic.so public.o
```



## 静态库和动态库

库: 包含一组功能函数或程序代码的二进制文件 

静态库在编译时被链接到目标文件中，而动态库在运行时被加载和链接  

分为静态库(.a)和动态库(.so)    

```cpp
[root@mengqi mengxiaoqi]# tree
.
├── app
│   └── demo01.cpp
└── tools
    ├── public.cpp
    └── public.h

[root@mengqi tools]# cat public.h
#include <iostream>
void func();
class AA
{
public:
void show();
};

[root@mengqi tools]# cat public.cpp 
#include "public.h"
using namespace std;
void func()
{
   cout << "调用了func()函数。\n";
}
void AA::show()
{
   cout << "梦小七。\n";
}

[root@mengqi app]# cat demo01.cpp 
#include "public.h"
using namespace std;
int main()
{
   func();
   AA a;	
   a.show();
}

[root@mengqi app]# g++ -o demo01 demo01.cpp ../tools/public.cpp -I ../tools/public.h【头文件所在目录】
[root@mengqi app]# ./demo01 
调用了func()函数。
梦小七。

/*创建静态库*/
//编译源文件位目标文件
[root@mengqi tools]# g++ -c public.cpp -o public.o 
//将目标文件创建为静态库文件
[root@mengqi tools]# ar rcs libpublic.a public.o
/*使用静态库*/
1.[root@mengqi app]# g++ -o demo01 demo01.cpp ../tools/libpulic.a
2.【推荐】[root@mengqi app]# g++ -o demo01 demo01.cpp  -L ../tools/ -l public

/*创建动态库*/
[root@mengqi tools]# g++ -fPIC -shared -o libpublic.so public.cpp
/*使用动态库*/
//设置动态库环境变量
[root@mengqi app]# export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/root/mengxiaoqi/tools
[root@mengqi app]# echo $LD_LIBRARY_PATH
:/root/mengxiaoqi/tools
[root@mengqi app]# g++ -o demo01 demo01.cpp -L ../tools/ -l public

注意:如果动态库和静态库同时存在,编译器将优先使用动态库
```

## gdb常用命令

如果希望程序可调试，编译时需要加-g 选项，并且，不能使用-o 的优化选项。

```cpp
//启动GDB调试指定的程序
gdb [program]

//退出GDB
q

//启动程序执行，可以指定命令行参数
r [可选参数]

//继续程序执行，直到下一个断点或程序结束
c

//单步执行，但不进入函数调用
n	

//设置断点
b [函数名、行号或源文件和行号]

//打印变量的值
p 变量

//修改变量的值
set 变量
```

![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1735608332515-8e880a16-3022-4574-8f89-d8436437b699.png)

- 当 Linux 中的程序崩溃时会生成一个  core 文件 , 用于调试程序
- 大多数Linux系统默认不生成core文件  
- Linux 缺省不会生成 core 文件，需要修改系统参数。
  调试 core 文件的步骤如下：
  1）用ulimit-a 查看当前用户的资源限制参数;
  2）用 ulimit -c unlimited 把 core file size 改为 unlimited ;
  3）运行程序，产生 core 文件；
  4））运行 gdb程序名core 文件名；
  5）在 gdb 中，用 bt 查看函数调用栈。

- 可调试正在运行的程序,调试时程序暂停

## main 函数

==int setenv(const char *name,const char *value,int overwrite);==

- name:环境变量名。
- value:环境变量的值。
- overwrite:

0-如果环境不存在，增加新的环境变量，如果环境变量已存在，不替换其值；

非0-如果环境不存在，增加新的环境变量，如果环境变量已存在，替换其值。↑
返回值：0-成功；-1-失败（失败的情况极少见）。注意：此函数设置的环境变量只对本进程有效，不会影响shell的环境变量。如果在运行程序时执行setenv()函数，进程终止后再次运行该程序，上次的设置是无效的。

```cpp
[root@mengqi test]# cat demo.cpp 
#include <iostream>
using namespace std;

int main(int argc, char *argv[], char *envp[])
{

  if(argc!=4)
  {
     cout << "参数不满足要求(3个)!";
     return -1;
  }

  cout << "程序满足要求!";

  //显示全部的环境变量
  for (int i=0;envp[i]!=0;i++)
  {
    cout << envp[i] << endl;
  }

  //设置环境变量AA
  setenv("HOSTNAME", "mengqi", 0);

  //显示环境变量AA的值
  cout << "HOSTNAME=" << getenv("HOSTNAME") << endl;

  return 0;
}
    
[root@mengqi test]# ./demo 33 3 34
程序满足要求!9
[root@mengqi test]# ./demo 33 3
参数不满足要求(3个)!
```

## 时间操作函数

- time_t用于表示时间类型，它是一个long类型的别名，在<time.h>文件中定义，表示从1970年

  1月1日0时0分0秒到现在的秒数。

- localtime_r  将时间戳格式转换为当地时间  <time.h>  

- `gettimeofday` 函数  微秒级别  测量程序的执行时间  	  <sys/time.h>  

- 程序挂起：<unistd.h>

  sleep()  秒

  usleep() 微秒

## Linux 目录操作

### 获取当前工作目录

包含头文件:<unistd.h>

```cpp
char *getcwd(char *buf, size_t size);
char *get_current_dir_name(void);
```

```cpp
示例:
#include<iostream>
#includee<unistd.h>
using namespace std;
int main()
{
	char path1[256];//linux系统目录的最大长度是255
	getcwd(path1,256);
	cout <<"path1="<<path11<<endl;
	char *path2=get_current_dir_name0;
	cout <<"path2="<<path2<< endl;
	free(path2);//注意释放内存。mallocnewdelete
}
```

### 切换工作目录

-  在程序内部修改工作目录，但不会影响外部 shell 环境的工作目录。  
-  包含头文件：<unistd.h>
-  int chdir(const char *path);
-  返回值：0-成功；其它-失败（目录不存在或没有权限）

### 创建目录

- 包含头文件：<sys/stat.h>
- int mkdir(const char *pathname, mode_t mode);
- pathname-目录名。
- mode-访问权限，如0755，不要省略前置的0。
- 返回值：0-成功；其它-失败（上级目录不存在或没有权限）

### 删除目录

![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1735634504496-87e6ab07-7f89-4ae3-a025-ec905b26625a.png)

### 获取目录中文件的列表



![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1735637121045-84505dbc-04a2-4cc2-bf67-9449a3d93cbf.png)

![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1735637105392-a6102bd3-ad53-4ccb-bb6a-b866c40988ec.png)

![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1735637143361-30c683a9-1f53-4faf-a246-a328644d78bf.png)

![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1735637273138-f79af324-db60-4c77-b8cf-4e1d3f5e93ca.png)![img](https://cdn.nlark.com/yuque/0/2024/png/46482601/1735637449388-15567928-8ee5-4ff8-9bfb-9250720f1ee7.png)

```cpp
[root@mengxiaoqi test]# cat demo3.cpp 
#include <iostream>
#include <dirent.h>
using std::cout;
using std::endl;

int main(int argc, char *argv[])
{
  if (argc !=2){
     cout << "命令行参数不符合要求\n";
     return -1;
  }

  DIR *dir; //定义目标指针

  //打开目录
  if ( (dir = opendir(argv[1])) == 0){
     return -1;
  }
  
  //存放读取到的内容
  struct dirent *stdinfo = nullptr;

  while(1){
  //读取一项内容并显示
  if ((stdinfo = readdir(dir)) == 0){
     break;
    }

  cout << "文件名:" << stdinfo->d_name << ", 文件类型:" << (int)stdinfo->d_type << endl;
  }

  //关闭目录指针
  closedir(dir);

  return 0;
}
```

## Linux 的系统错误	

在C++程序中，如果调用了库函数，可以通过函数的返回值判断调用是否成功。

还有一个整型的全局变量errno，存放了函数调用过程中产生的错误代码。

如果调用库函数失败，可以通过errno 的值来查找原因，这也是调试程序的一个重要方法。

errno在<errno.h>中声明。

配合strerror()和perror()两个库函数，可以查看出错的详细信息。

### strerror()库函数

```cpp
#include <iostream>
#include <cstring>
using std::cout;
using std::endl;

int main()
{

  //1success_info+130error_info	
  for (int i=0; i<131; i++){
     cout << i << ":" << strerror(i) << endl;
  }  
    
  return 0;
}	
```

### perror()

perror()在<stdio.h>中声明,用于在控制台显示最近一次系统错误的详细信息，在实际开发中，服务程序在后台运行，通过控制台显示错误信息意义不大。（对调试程序略有帮助）

void perror(const char *s);

### access()

- 主要用于判断目录或文件是否存在


### stat结构体

- 存放目录或文件的详细信息

- 重点关注st_mode、st_size和st_mtime成员

  ```cpp
  #include <iostream>
  #include <sys/stat.h>
  #include <unistd.h>  // For stat()
  #include <cstring>   // For strerror
  int main() {
      const char* filepath = "/root/1";  // 文件路径
      struct stat fileStat;
  
      // 检查文件类型
      if (S_ISREG(fileStat.st_mode)) {
          cout << "文件类型: 常规文件" << endl;
      } else if (S_ISDIR(fileStat.st_mode)) {
          cout << "文件类型: 目录" << endl;
      }  else {
          cout << "文件类型: 其他" << endl;
      }
  
      // 检查文件权限
      cout << "文件权限: ";
      if (fileStat.st_mode & S_IRUSR) cout << "读 " << endl;
      if (fileStat.st_mode & S_IWUSR) cout << "写 " << endl;
      if (fileStat.st_mode & S_IXUSR) cout << "执行 " << endl;
  
      cout << "文件大小: " << fileStat.st_size << " 字节" << endl;
  
      cout << "最近访问时间: " << ctime(&fileStat.st_atime);
  
      cout << "最近修改时间: " << ctime(&fileStat.st_mtime);
  
      cout << "最近状态改变时间: " << ctime(&fileStat.st_ctime);
  
      return 0;
  }
  ```


### stat()库函数

包含头文件：
#include <sys/stat.h>

函数声明：
int stat(const char *path, struct stat *buf);

stat()函数获取path参数指定目录或文件的详细信息，保存到buf 结构体中。

返回值：0-成功，-1-失败，errno被设置。

### utime()库函数

- 修改目录或文件的时间。

包含头文件：

#include <sys/types.h>
#include <utime.h>

函数声明：

```cpp
int utime(const char *filename, const struct utimbuf *times);
```

- 修改参数filename的st_atime和st_mtime。

如果参数times为空指针，则设置
为目前时间。

结构utimbuf 声明如下：

```cpp
struct utimbuf
{
	time_t actime;
	time_t modtime;
}
```

返回值：0-成功，-1-失败，errno 被设置。

### rename()库函数

- 重命名目录或文件，相当于操作系统的my命令。

  包含头文件：#include<stdio.h>

  函数声明：
  
  ```cpp
  int rename(const char **oldpath.const char* *newpath);
  ```
  
  参数说明：pathname 待删除的目录或文件名
  返回值：0-成功，-1-失败，errno被设置。

## Linux的信号

### 信号的基本概念

- 信号（signal）是软件中断，是进程之间相互传递消息的一种方法，用于通知进程发生了事件，但是，不能给进程传递任何数据。

- 信号产生的原因有很多，在 Shell中，可以用 kill 和killall 命令发送信号：

  kill-信号的类型进程编号

  killall-信号的类型进程名

### 信号的类型



![img](https://cdn.nlark.com/yuque/0/2025/png/46482601/1735779385868-8d2542e0-ccc3-448f-9e3b-dd50a565ff4e.png)![img](https://cdn.nlark.com/yuque/0/2025/png/46482601/1735779392934-bdaa7005-72bd-4bc7-9359-9ca26a6bd2b9.png)![img](https://cdn.nlark.com/yuque/0/2025/png/46482601/1735779443954-3c172613-1e3f-42ec-8df3-088596db8ec8.png)

![img](C:\Users\梦小七\AppData\Roaming\Typora\typora-user-images\image-20250112123025849.png)

`killall -15` 命令的作用是向所有名为指定进程的进程发送 `SIGTERM`（信号 15），请求它们正常终止。![img](https://cdn.nlark.com/yuque/0/2025/png/46482601/1735779464577-4022e000-8089-4e84-b96d-21f10b900fe7.png)

![img](https://cdn.nlark.com/yuque/0/2025/png/46482601/1735779511066-fa316d79-68bf-4895-9546-d87ea89485cf.png)

### 信号的作用

- 服务程序运行在后台，如果想让中止它，杀掉不是个好办法，因为进程被杀的时候，是突然死亡，没有安排善后工作。
- 如果向服务程序发送一个信号，服务程序收到信号后，调用一个函数，在函数中编写善后的代码，程序就可以有计划的退出。
- 向服务程序发送0的信号，可以检测程序是否存活。

### 信号的处理  

进程对信号的处理方法有三种：←
1）对该信号的处理采用系统的默认操作，大部分的信号的默认操作是终止进程
2）设置信号的处理函数，收到信号后，由该函数来处理。
3）忽略某个信号，对该信号不做任何处理，就像未发生过一样。

### signal()函数

- 设置程序对信号的处理方式

函数声明：

```cpp
sighandler_t signal(int signum, sighandler_t handler);
```

参数signum表示信号的编号。
参数handler表示信号的处理方式，有三种情况：
1）SIG_DFL：恢复参数 signum 所指信号的处理方法为默认值。
2）一个自定义的处理信号的函数，信号的编号为这个自定义函数的参数。
3）SIG_IGN：忽略参数 signum 所指的信号。

### 发送信号

Linux操作系统提供了kill和killall命令向程序发送信号，在程序中，可以用kill()库函数向其它进程发送信号。

函数声明：

```cpp
int kill(pid_t pid, int sig);
```

kill()函数将参数sig指定的信号给参数pid指定的进程。

参数pid 有几种情况：

1） pid>0 将信号传给进程号为 pid 的进程。

2）pid=0 将信号传给和目前进程相同进程组的所有进程，常用于父进程给子进程发送信号，注意，发送信号者进程也会收到自己发出的信号。

3）pid=-1将信号广播传送给系统内所有的进程，例如系统关机时，会向所有的登录窗口广播关机信息。

sig：准备发送的信号代码，假如其值为0则没有任何信号送出，但是系统会执行错误检查，通常会利用sig值为零来检验某个进程是否仍在运行。

返回值说明：成功执行时，返回0；失败返回-1，errno被设置。

## 进程终止

### 中止方式

- 5种正常终止：

1）在main()函数用月return返回；

2）在任意函数中调用exit()函数；

3)在任意函数中调用_exit()或_Exit()函数；

4）最后一个线程从其启动例程（线程主函数）用return返回

5）在最后一个线程中调用pthread_exit()返回;

- 3种异常终止：

6)调用abort()函数中止；

7）接收到一个信号；

8)最后一个线程对取消请求做出响应

### 进程终止的状态

- 在 main()函数中，return的返回值即终止状态，如果没有return 语句或调用exit()，那么该进程的

  终止状态是 0。

- 在 Shell 中，查看进程终止的状态：==echo $?==

正常终止进程的3个函数（exit()和_Exit()是由ISO C说明的，_exit()是由 POSIX说明的）。
void exit(int status);
void _exit(int status);
void _Exit(int status);
status 也是进程终止的状态。
如果进程被异常终止，终止状态为非0。

###  资源释放的问题

- return表示函数返回，会调用局部对象的析构函数，main()函数中的return还会调用全局对象的析

  构函数。

- exit()表示终止进程，不会调用局部对象的析构函数，只调用全局对象的析构函数。

- exit()会执行清理工作，然后退出，exit()和Exit()直接退出，不会执行任何清理工作。

### 进程的终止函数

进程可以用 atexit()函数登记终止函数（最多 32个）

这些函数将由exit()自动调用

int atexit(void (*function)(void));

exit()调用终止函数的顺序与登记时相反。 进程退出前的收尾工作

## 调用可执行程序

- Linux提供了 system()函数和 exec 函数族，在C++程序中，可以执行其它的程序（二进制文件、

  操作系统命令或 Shell脚本）。

#### ==system()函数==

- system()函数提供了一种简单的执行程序的方法，把需要执行的程序和参数用一个字符串传给

  system()函数就行了。

函数的声明：

int system(const char* string);

system()函数的返回值比较麻烦。

> [!NOTE]
>
> 1）如果执行程序失败，system()函数返回非0；
>
> 2）如果执行程序成功，并且被执行的程序终止状态是0，system()函数返回0；
>
> 3）如果执行程序成功，并且被执行的程序终止状态不是0，system()函数返回非0。

#### ==exec函数族==

exec 函数族提供了另一种在进程中调用程序（二进制文件或 Shell脚本）的方法
exec 函数族的声明如下：

```cpp
int execl(const char *path, const char *arg, ..);

int execlp(const char *file, const char *arg, ..);

int execle(const char *path, const char *arg.., char * const envp);

int execv(const char *path, char *const argv[l);

int execvp(const char *file, char *const argv[]);

int execvpe(const char *file, char *const argv[],char *const envp[]);
```

> [!CAUTION]
>
> 1）如果执行程序失败则直接返回-1，失败原因存于error 中。
>
> 2）新进程的进程编号与原进程相同，但是，新进程取代了原进程的代码段、数据段和堆栈。
>
> 3）如果执行成功则函数不会返回，当在主程序中成功调用exec后，被调用的程序将取代调用者程序，也就是说，exec 函数之后的代码都不会被执行。
>
> 4）在实际开发中，最常用的是execl()和 execv()，其它的极少使用。

## 创建进程

### Linux 的0、1和2号进程

整个linux系统全部的进程是一个树形结构。

0号进程（系统进程）是所有进程的祖先，它创建了1号和2号进程。

1号进程（systemd）负责执行内核的初始化工作和进行系统配置。

2号进程（kthreadd）负责所有内核线程的调度和管理。

用pstree命令可以查看进程树(yum -y install psmisc)

pstree -p 进程编号

### 进程标识

- 每个进程都有一个非负整数表示的唯一的进程ID。

- 虽然是唯一的，但是进程ID 可以复用。当一个进程终止后，其进程ID 就成了复用的候选者。Linux采用延迟复用算法，让新建进程的ID不同于最近终止的进程所使用的ID。这样防止了新进程被误认为是使用了同一ID的某个已终止的进程。

  ```cpp
  pid_t getpid(void);// 获取当前进程的 ID。
  pid t getppid(void);//获取父进程的ID。
  ```


### fork()函数

一个现有的进程可以调用fork()函数创建一个新的进程。

```c++
pid_t fork(void);
```

由fork()创建的新进程被称为子进程。

父进程和子进程都从调用fork()之后的代码开始执行

fork()函数被调用一次，但返回两次。

两次返回的区别是子进程的返回值是0，而父进程的返回值则是新建子进程的进程 ID。
子进程和父进程继续执行fork()之后的代码，子进是父进程的副本。
子进程获得了父进程数据空间、堆和栈的副本（注意：子进程拥有的是副本，不是共享）。

fork()之后，父进程和子进程的执行顺序是不确定的

```cpp
pid_t pid = fork();
if (pid>0)
{
cout << "父进程:" << pid << endl;
} else {
cout << "子进程:" << pid << endl;
}
```

### fork()的两种用法

1）父进程复制自己，然后，父进程和子进程分别执行不同的代码。

这种用法在网络服务程序中很常见，父进程等待客户端的连接请求，当请求到达时，父进程调用fork()，让子进程处理些请求，而父进程则继续等待下一个连接请求。

```cpp
int main()
{
if (fork()>0)
{
while (true){
sleep(1) ;
cout<<"父进程运行中...\n";
}else
{//子进程将执行这段代码。
sleep(10) ;
cout<<"子进程开始执行任务...\n"；
execl ("/bin/ls","/bin/ls", "-It","/tmp", 0) ;
cout << "子进程执行任务结束，退出。\n"；
}
```

2）进程要执行另一个程序。这种用法在 Shell 中很常见，子进程从 fork()返回后立即调用 exec。

### 共享文件

fork()的一个特性是在父进程中打开的文件描述符都会被复制到子进程中，父进程和子进程共享同一个文件偏移量。

如果父进程和子进程写同一描述符指向的文件，但又没有任何形式的同步，那么它们的输出可能会相互混合

```cpp
#include <iostream>
#include <unistd.h>
#include <fstream>
using std::cout;
using std::endl;
using std::ofstream;

int main()
{
    ofstream fout;

    fout.open("/root/mengxiaoqi/meng.txt"); // 打开文件
   
    fork();

    for (int i = 0; i < 100; i++) {
        fout << "梦小七" << endl;
    }

    fout.close();

    return 0;
}

[root@mengxiaoqi mengxiaoqi]# wc meng.txt 
 200  200 2000 meng.txt
```

### vfork()函数

- vfork()函数的调用和返回值与fork()相同，但两者的语义不同。
- vfork()函数用于创建一个新进程，而该新进程的目的是exec一个新程序，它不复制父进程的地址空间，因为子进程会立即调用exec，于是也就不会使用父进程的地址空间。如果子进程使用了父进程的地址空间，可能会带来未知的结果。
- vfork()和fork()的另一个区别是：vfork()保证子进程先运行，在子进程调用exec或exit()之后父进程才恢复运行。

## 僵尸进程

如果父进程比子进程先退出，子进程将被1号进程托管(这也是一种让程序在后台运行的方法)。

如果子进程比父进程先退出，而父进程没有处理子进程退出的信息，那么，子进程将成为僵尸进程。 

僵尸进程有什么危害？内核为每个子进程保留了一个数据结构，包括进程编号、终止状态、使用CPU时间等。父进程如果处理了子进程退出的信息，内核就会释放这个数据结构，父进程如果没有处理子
进程退出的信息，内核就不会释放这个数据结构，子进程的进程编号将一直被占用。系统可用的进程编
号是有限的，如果产生了大量的僵尸进程，将因为没有可用的进程编号而导致系统不能产生新的进程。

僵尸进程的避免：

1）子进程退出的时候，内核会向父进程发头SIGCHLD信号，如果父进程用 signal(SIGCHLD,SIG_IGN)通知内核，表示自己对子进程的退出不感兴趣，那么子进程退出后会立即释放数据结构。

2）父进程通过wait()/waitpid()等函数等待子进程结束，在子进程退出之前，父进程将阻塞等待。

```cpp
pid_t wait(int *stat_loc);

pid_t waitpid(pid_t pid, int *stat_loc, int options);

pid_t wait3(int *status,int options, struct rusage *rusage); 

pid_t wait4(pid_t pid, int *status, int options, struct rusage *rusage)

返回值是子进程的编号。
   
stat_loc是子进程终止的信息：
  a）如果是正常终止，宏WIFEXITED(stat_loc)返回真，宏WEXITSTATUS(stat_loc)可获取终止状态；
  b）如果是异常终止，宏WTERMSIG(stat_loc)可获取终止进程的信号。
```

查看子进程退出状态

```cpp
#include <iostream>
#include <unistd.h>
#include <sys/wait.h>
using std::cout;
using std::endl;

int main()
{
  if (fork()>0)
  {
    int sts;
    pid_t pid = wait(&sts);

    cout << "已终止的子进程编号是:" << pid << endl;

    if (WIFEXITED(sts))
    {
      cout << "子进程是正常退出的,退出状态是:" << WEXITSTATUS(sts) << endl;
    } else {
      cout << "子进程是异常退出的,终止它的信号是:" << WTERMSIG(sts) << endl;
    }
  } else {
      sleep(5);
      exit(0);
    }
return 0;
}
```

## 多进程与信号

在多进程的服务程序中，如果子进程收到退出信号，子进程自行退出，如果父进程收到退出信号，应该先向全部的子进程发送退出信号，然后自己再退出。

```cpp
#include <iostream>
#include <unistd.h>
#include <signal.h>
using namespace std;

void FathEXIT(int sig);  //父进程的信号函数
void ChldEXIT(int sig);  //子进程的信号函数

int main()
{
  //忽略全部的函数,不希望被打扰
  for(int i=1;i<=64;i++) signal(i,SIG_IGN);

  //设置信号,在shell状态下可用“kill 进程号”或“Ctrl+c”正常终止进程
  //不要使用“kill -9 进程号”强行终止
  signal(SIGTERM,FathEXIT); signal(SIGINT,FathEXIT); //SIGTERM 15 SIGINT 2

  while (true)
  {
    if (fork()>0)  //父进程
    {
      sleep(5); continue;
    } 
    else  //子进程
    {
      //子进程需要重新设置信号
      signal(SIGTERM,ChldEXIT);  //子进程的退出函数和父进程不一样
      signal(SIGINT ,SIG_IGN);   //子进程不需要捕获SIGINT信号

      while (true)
      {
        cout << "子进程" << getpid() << "正在运行中...\n"; sleep(3); continue;
      }
    }
  } 
  return 0;
}

//父进程的信号处理函数
void FathEXIT(int sig)
{
  //防止信号处理函数在执行的过程中再次被信号中断
  signal(SIGINT,SIG_IGN); signal(SIGTERM,SIG_IGN);

  cout << "父进程退出,sig=" << sig << endl;

  kill(0,SIGTERM); //向全部的子进程发送15的信号,通知它们退出

  //释放资源
  exit(0);
}

//子进程的信号处理函数
void ChldEXIT(int sig)
{
  //防止信号处理函数在执行的过程中再次被信号中断
  signal(SIGINT,SIG_IGN); signal(SIGTERM,SIG_IGN);

  cout << "子进程" << getpid() << "退出,sig=" << sig << endl;

  //释放资源
  exit(0);
}
```

## 共享内存

多线程共享进程的地址空间，如果多个线程需要访问同一块内存，用全局变量就可以了。

在多进程中，每个进程的地址空间是独立的，不共享的，如果多个进程需要访问同一块内存，不能用全局变量，只能用共享内存。

共享内存（SharedMemory）允许多个进程（不要求进程之间有血缘关系）访问同一块内存空间，是多个进程之间共享和传递数据最高效的方式。进程可以将共享内存连接到它们自己的地址空间中，如果某个进程修改了共享内存中的数据，其它的进程读到的数据也会改变。

共享内存没有提供锁机制，也就是说，在某一个进程对共享内存进行读/写的时候，不会阻止其它进程对它的读/写。如果要对共享内存的读/写加锁，可以使用信号量。

Linux中提供了一组函数用于操作共享内存。

### shmget函数

shmget()函数用来获取/创建共享内存。

```cpp
int shmget(key_t key, size_t size, int shmflg);
```

参数key是共享内存的键值，是一个整数，一般采用十六进制，typedef unsigned int key_t，是共享内存在系统中的编号，不同共享内存的编号不能相同。

参数 size 是待创建的共享内存的大小，以字节为单位。

shmflg共享内存的访问权限，与文件的权限一样，例如0666|IPC_CREAT，0666表示全部用户对它可读写，IPC_CREAT表示如果共享内存不存在，就创建它。

返回值：成功返回共享内存的id（一个大于0的整数），失败返回-1(系统内存不足、没有权限)

- 用ipcs-m 可以查看系统的共享内存，包括：键值（key），共享内存id（shmid），拥有者（owner），权限（perms），大小（bytes）。
- 用ipcrm-m共享内存id可以手工删除共享内存。

### shmat函数

该函数用于把共享内存连接到当前进程的地址空间。

```cpp
void *shmat(int shmid, const void *shmaddr, int shmflg);
```

shmid由 shmget()函数返回的共享内存标识。

shmaddr指定共享内存连接到当前进程中的地址位置，通常填0，表示让系统来选择共享内存的地址。
shmflg标志位，通常填0。

调用成功时返回共享内存起始地址，失败返回(void*)-1.

### shmdt函数

该函数用于将共享内存从当前进程中分离，相当于shmat()函数的反操作。

```cpp
int shmdt(const void *shmaddr);
```

shmaddr  shmat()函数返回的地址。

调用成功时返回 0，失败时返回-1。

### shmctl函数

该函数用于操作共享内存，最常用的操作是删除共享内存。

```cpp
int shmctl(int shmid,int command, struct shmid_ds *buf);
```

shmidshmget()函数返回的共享内存id。

command操作共享内存的指令，如果要删除共享内存，填IPC_RMID。

buf操作共享内存的数据结构的地址，如果要删除共享内存，填0。

调用成功时返回0，失败时返回-1。

注意，用root创建的共享内存，不管创建的权限是什么，普通用户无法删除。

```cpp
#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <unistd.h>
#include <sys/ipc.h>
#include <sys/shm.h>
using namespace std;

struct mengxiaoqi
{
  int id;        //编号
  char name[51];  //姓名
};

int main(int argc,char *argv[])
{
  if (argc!=3) { cout << "Using:./demo no name\n"; return -1; }
  
  //第一步:创建/获取共享内存,键值key为0x5005
  int shmid=shmget(0x5005, sizeof(mengxiaoqi), 0640|IPC_CREAT);

  if (shmid == -1)
  {
    cout << "shmget(0x5005) failed.\n"; return -1;
  }

  cout << "shmid=" << shmid << endl;
  
  //第二步:把共享内存连接到当前进程的地址空间
  mengxiaoqi *ptr=(mengxiaoqi *)shmat(shmid,0,0);
  if (ptr == (void *)-1)
  {
    cout << "shmat() failed\n"; return -1;
  }
  
  //第三步:使用共享内存,对共享内存进行读/写
  cout << "原值:id=" << ptr->id << ",name:" << ptr->name << endl;
  ptr->id=atoi(argv[1]);
  strcpy(ptr->name,argv[2]);
  cout << "新值:id=" << ptr->id << ",name:" << ptr->name << endl;
  
  //第四步:把共享内存从当前进程中分离
  shmdt(ptr);
  
  //第五步:删除共享内存
  if (shmctl(shmid,IPC_RMID,0)==-1)
  {
    cout << "shmctl faild\n; return -1";
  }

  return 0;
}
```

## 循环队列

共享内存不能自动扩展，只能采用C++内置的数据类型。

共享内存不能采用STL容器，也不能使用移动语义。

如果要实现多进程的生产/消费者模型，只能采用循环队列。
