## Solidity的学习01

### 1. Solidity 智能合约许可证

在 Solidity 源文件的顶部添加机器可读许可证，如下所示：

```
// SPDX-License-Identifier: MIT
```

MIT 许可证类似于在GitHub 上找到的许可证。如果不想在 Solidity 源文件上指定许可证，则可以添加`UNLICENSED`值，但这不应留空。

### 2. Solidity Pragma

一个 pragma 指令指示 Solidity 编译器运行智能合约的版本。

下面的 pragma 指令显示智能合约是为 Solidity 版本 0.8.13 编写的。^符号表示 Solidity 程序不应与低于 0.8.0 的版本或以 0.9.0 开头的版本一起使用。

```
pragma solidity ^ 0.8.13;
```

pragma 指令始终位于源文件的本地，这意味着必须将其添加到所有源文件中。

### 3. Solidity 合约

合约是部署在区块链上指定地址的状态和功能的集合。

```
contract My_Smart_Contract {}
```

### 4. Solidity 中的变量

Solidity 是一种静态类型的编程语言，这意味着 Solidity 程序中的状态和局部变量必须由程序员在编译智能合约之前声明。

下面是在 Solidity 中声明变量的示例：

```
string public myName;
```

定义的变量初始化如下：

```
myName = "Samuel";
```

上面的变量可以像这样声明和初始化：

```
string myName = "Samuel";
```

Solidity 中有 3 种主要类型的变量：局部变量、状态变量和全局变量。

#### 1.局部变量

这些是在solidity 函数中声明的变量，它们不存储在区块链上。

#### 2.状态变量

状态变量是在solidity 函数之外声明的变量，它们永久存储在区块链上。

#### 3. 全局变量

Solidity 全局变量是其他函数可以访问的变量。它们保存有关区块链及其交易属性的信息。

### 5. Solidity 构造函数

在 Solidity 中，构造函数是一个特殊的关键字，用于创建一个可选函数，用于初始化智能合约中的状态变量。

```
constructor() {
     myName = "Samuel";
}
```

智能合约只能有一个构造函数，并且只有在编译智能合约后才会执行。

### 6. Solidity 函数

在编程中，函数是执行任务的代码块。它们是封装在单个对象中的代码组件。

该`function`关键字用于在 Solidity 中创建函数，类似于在 JavaScript 中创建函数的方式。

```
function showMyName() public view returns (string memory) {}
```

Solidity 函数分解：

-  `public`关键字表示该函数可以被其他合约访问。
-  `view` 关键字表示该函数在区块链上是只读的，它不会改变区块链上的数据。
-  `returns`关键字表示函数返回的数据类型。
-  `string`关键字指定返回值的数据类型。
-  `memory`关键字意味着函数的变量将在函数被调用时存储在一个临时位置。

### 7. Solidity 字符串连接

连接通常是将一个字符串连接到另一个字符串末尾的过程。串联在任何编程语言中都是一个非常重要的概念。

在 Solidity 中连接一个字符串与使用流行的`+`符号连接两个或多个字符串有很大不同。

在 Solidity 中，使用一种调用方法`abi`来连接两个或多个字符串。这`abi`是*Application Binary Interface*的缩写形式，它允许我们将参数编码或解码为 ABI。

```
string a = "A ";
string b = "B ";
string c = "C ";

string(abi.encodePacked(a, b, c));
```

上面的代码将给出以下输出：

```
A B C
```

#### 8.简单的智能合约示例

以下是一个简单的 Solidity 智能合约示例：

```solidity
// 1. SPDX-License-Identifier: MIT

// 2. Solidity Version
pragma solidity ^ 0.8.13;

// 3. Contract
contract My_Smart_Contract {

    // 4. Contract state declaration
    string public myName;

    // 5. Constructor to initialize value to declared states
    constructor() {
        myName = "Samuel";
    }

    // 6. Contract function
    function showMyName() public view returns (string memory) {
        return myName;
    }
}
```