## Solidity学习笔记

——202 赵一谦

#### Solidity源文件结构

SPDX 版权许可标识

由于智能合约的开源的源代码经常涉及版权问题，solidity要求每个源文件交代

SPDX 版权许可标识，如现在最经常使用的：

//SPDX-License-Identifier:MIT

如果源代码不开源，则使用特殊值 UNLICENSED

Pragma和版本标识

在声明SPDX后，就应该声明solidity语言版本，否则将无法编译，如下：

pragma solidity ^0.8.16;

^0.8.16 可编译0.8.16及以上版本

=0.8.16 可编译0.8.16版本

\>=0.8.16 等同^0.8.16

<=0.8.16 可编译0.8.16及以下版本

注释

//或/* */ 代码注释

/** */ 文档注释

#### 类型

##### 值类型

布尔型

bool:取值为true或者false

特别注意的是，当布尔型使用运算符||和&&时，遵循短路原则，及当a||b,

a==true时，直接判断a||b为true,因为不管b取值如何，不影响结果。同理，当a&&b,a==false,跳过b，直接判断，a&&b为false.

整形

Int(int256)/uint(uint256),相较于其他语言，多了uint：无符号整型，int256范围：-2^255~2^255-1;uint范围：0~2^256.

以及 int8 到 int256，以 8 位为步长递增。

整形 X，可以使用 type(X).min/type(X).max 去获取这个类型的最小值/最大值

移位

X<<y=x*2**y

x>>y=x/2**y

定长浮点型

fixed / ufixed：表示各种大小的有符号和无符号的定长浮点型。

在其他语言中，浮点型和solidity中定长浮点型之间最大的不同点是， 在前者中整数部分和小数部分需要的位数是可变的，而后者中这两部分的长度受到严格的规定。 一般来说，在浮点型中，几乎整个空间都用来表示数字，但只有少数的位来表示小数点的位置。

地址类型

address：保存一个20字节（160bit）的值。

address payable ：可支付地址，与 address 相同，不过有成员函数 transfer 和 send ,相较于address,其可以接收以太币。

address payable 可以隐式转换为 address ，从 address 到 address payable 必须显示的转换, 通过 payable(<address>) 进行转换。

可以使用 balance 属性来查询一个地址的余额， 也可以使用 transfer 函数向一个可支付地址发送 以太币。

 

定长字节数组

bytes1,bytes2,bytes3,...bytes32

.length读取长度

 

枚举类型

enum 名字 { , , }

 

用户定义值类型

Type a is b ,a 是用户定义的类型的名称， b 必须是内置的值类型.

a.wrap从底层类型转换到自定义类型

a.unswap从自定义类型转换到底层类型

 

函数类型

分为外部类型和内部类型（默认）。内部函数只能在本合约使用，外部类型，可以通过外部函数调用。

形式：

```solidity
function (<parameter types>) {internal|external} [pure|constant|view|payable] [returns (<return types>)]
```



既然是函数类型，所有函数也可以作为参数引入和返回。

pure:只可调用，不可读写

view:可读

payable:可接收ETH

 

#### 引用类型

分为 结构体，数组，映射，

有三种存储类型：

memory:数据在内存中，数据仅在函数调用期间有效。不能用于外部调用。

storage:状态变量保存的位置，只要合约存在就一直存储．

calldata:保存函数参数的特殊数据位置，是一个只读位置。(暂时也不太懂QVQ)

数组

形如a[b]（a为数据类型，b为数组长度）的叫数组。不声明b就为动态数组。这里和其他语言不一样的是，如uint[][3]是指声明一个3个元素的二维数组。java里是uint[3][].

a.length 数组长度

a.push（b） 向a数组末尾添加数值b，不写b默认为0

a.pop() 删除且不返回数值最后一个值。

切片查找：a[start:end]第一个元素为a[start],最后一个为a[end-1](和python相似)

结构体

不太方便记录下来，就是类似于下面代码，成功创建了两个结构体：

```solidity
//SPDX-License-Identifier:MIT

pragma solidity ^0.8.16;

contract SimpleStruct{

//学生

struct Student{

string name;

uint number;

}

 

//班级

struct Class{

string ClassName;

Student[] students;

mapping(string=>Student)index;

}

}
```



![img](file:///C:\Users\SHAY\AppData\Local\Temp\ksohtml9788\wps1.jpg) 

映射

mapping(KeyType => ValueType) 其中 keytype 可以是任何基本类型

其中valuetype可以是任何基本类型

 

综合使用以上知识，写个练习

![img](file:///C:\Users\SHAY\AppData\Local\Temp\ksohtml9788\wps2.jpg) 

![img](file:///C:\Users\SHAY\AppData\Local\Temp\ksohtml9788\wps3.jpg) 

