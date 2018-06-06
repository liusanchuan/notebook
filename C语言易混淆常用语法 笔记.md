#C语言易混淆常用语法 笔记
[TOC]
###  问号表达式
`A?B:C`
A为真则B，假则C
### 类型转换
`k1=tan(((double)i)/180.0*3.14);` 哎呀，坑啊，
### sizeof（）
返回 对象在内存空间中所占的大小，单位是字节；
- 可使用类型做参数`sizeof（int）`
- 使用变量做参数`sizeof（a）`，求数组元素的个数`sizeof(a)/sizeof(type a)`
### switch(表达式){ 
```
switch(表达式){ 
    case 常量表达式1:  语句1; break；
    case 常量表达式2:  语句2;
    … 
    case 常量表达式n:  语句n;
    default:  语句n+1;
}
```
### 向上向下取整
- `floor`向下取整 `floor（2.5）=2`，`floor（-2.5）=3`
- `ceil`（装天花板）向上取整 `ceil（2.5）=3`，`ceil（-2.5）=2`
- `（int）`强制类型转换，直接去掉小数部分
### [boost::format](https://www.boost.org/doc/libs/1_66_0/libs/format/doc/format.html)

 - 引入 `#include <boost/format.hpp>`
 - 控制输出 `std::cout <<boost::format("%1%\n %2%\n %3%")%"first"%12%a[1];`， 其中`%1%`代表后面的第一个参数，`%2%`第二个.. ,后面的`%"first"`就是输入给`1`的参数。
 - 也可以先定义后填入值,显得非常之方便啊。

~~~
     boost::format fmt("%1%\n %2%\n %3%");
    fmt% "first";
    fmt%12%a[1];
    std::cout<<fmt;
~~~
- 与`std::string` 转换，`string s=fmt.str()`,或者`string s=str(fmt)`.
- 也可使用标准输出 `std::cout<<boost::format("%s  %d  \n")%"toto:" %12.5;`

