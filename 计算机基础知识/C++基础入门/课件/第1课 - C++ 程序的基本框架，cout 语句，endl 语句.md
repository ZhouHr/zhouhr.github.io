# 20250104 - 认识 C++ 程序的基本框架，cout 语句，endl 语句

认识 C++ 程序的基本框架，cout 语句，endl 语句

## 知识点

### 程序的基本框架

```c
// 头文件，引入指令，不用英文分号（;）结束，作用就是告诉程序要带哪些工具包
// 由 “#include <包名>” 组成，iostream 是输入输出流包
#include <iostream>

// 使用的命名空间，指定使用std命名空间的语句，注意要以英文分号（;）结束，作用就是告诉程序要使用工具包的哪个工具
// 由 “using namespace 命名空间名;” 组成
using namespace std;

// 主函数，程序的入口，由 “int main() {  }” 组成，注意不需要英文分号（;）结束，英文“{}” 是代码块的开始和结束
int main() {

    // ... 代码块 ...

    // 返回值，由 “return 0;” 组成，注意要以英文分号（;）结束，0 表示程序正常结束
    return 0;                 
}
```


###  认识 cout 语句

让程序输出内容，由 “cout << "内容";” 组成，注意要以英文分号（;）结束，内容可以是字符串、变量、表达式等。
- 字符串：内容用英文双引号（""）包裹，例如：cout << "Hello, World!";
- 变量：内容用变量名，例如：cout << age;
- 表达式：内容用表达式，例如：cout << 1 + 1;

```c
#include <iostream>
using namespace std;

int main() {
    cout << "Hello, World!";
    # cout << age;
    # cout << 1 + 1;

    return 0;
}
```

###  认识 endl 语句

endl 是换行符，让程序输出换行符，由 “cout << endl;” 组成，也可以在输出其它内容的后面直接跟上 “<< endl” 进行换行符的输出。

```c
#include <iostream>
using namespace std;

int main() {
    cout << "我是谁?";
    cout << "你是谁?";
    cout << endl;
    cout << "他是谁?" << endl;
    cout << "你们是谁?" << endl;
    return 0;
}
```

### 注意点

- C++ 源码文件要保存并且保存的命名只能以 英文 和 数字 组成，不能使用中文、空格、特殊符号等，如：123.cpp、abc.cpp、abc123.cpp、abc_123.cpp 等。
- C++ 源码文件的后缀以 .cpp 结尾。
- 指令不需要英文分号（;）结束。
- 语句需要英文分号（;）结束。

## 作业

### 请使用 cout 语句、 endl 语句来完成一个小程序，输出内容格式如下：
我的名字是***
我今年***岁了
我今年读*年级了


格式里面的 * 表示用自己实际的情况替换。

### 作业改错，请指出下面代码错误的地方并修改正确：

```c
#include <iostream>

using namespace std

int main() {

	cout << "我是一位小学生。" << endl;

	cout << "我要开始学习C++了。 << endl";

	cout << "我要持续性的学习以便精通c++编程。" >> endl;
}
```