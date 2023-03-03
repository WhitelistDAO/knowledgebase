# **Solidity-数据位置及变量数据位置规则**

## 1.**数据位置**

在合约中声明和使用的变量都有一个数据位置，指明变量值应该存储在哪里。合约变量的数据位置将会影响Gas消耗量。

Solidity 提供 3 种类型的数据位置。

• storage

• memory

• calldata

### 1.1 **storage**

存储位置 storage 用来存储永久数据，可以被合约中的所有函数访问。

storage 变量，通常用于存储智能合约的状态变量，它在函数调用之间保持持久性。

storage 与其他数据位置相比，成本较高。

我们可以把它理解为计算机的硬盘数据，所有数据都永久存储。

### 1.2 **memory**

存储位置 memory 用来存储临时数据，比 storage 便宜。

memory 变量，通常用于保存临时变量，以便在函数执行期间进行计算。一旦函数执行完毕，它的内容就会被丢弃。它只能在所在的函数中访问。

我们可以把它理解为每个单独函数的内存 RAM。

### 1.3 **calldata**

calldata 是不可修改的非持久性数据位置，类似于 memory，但只能出现在函数的输入参数位置。

外部函数 external function 的传入参数强制为 calldata 类型。

使用 calldata 的好处是在内部函数中作为输入参数传递时，省掉了数据拷贝的成本，节省 gas 费。memory 作为参数时，需要进行数据拷贝。

## 2. **变量数据位置规则**

### 2.1 **规则1 – 状态变量**

状态变量总是存储在storage中。

```
Solidity
// SPDX-License-Identifier: MIT 
pragma solidity ^0.8.0; 
contract DataLocation {   
// storage    
uint stateVariable;    
uint[] stateArray;  
}  

此外，不能显式地标记状态变量的位置。

Solidity
// SPDX-License-Identifier: MIT 
pragma solidity ^0.8.0; 
contract DataLocation {   
uint storage stateVariable; // 错误  
uint[] memory stateArray; // 错误 
} 
```



### 2.2 **规则2 – 函数参数与返回值**

函数参数包括返回参数都存储在memory中。

```
Solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0; 
contract DataLocation {    
// storage      
uint stateVariable;   
uint[] stateArray;    
function calculate(uint num1, uint num2) 
public pure returns (uint result) {   
return num1 + num2;  
} 
} 
```

此处，函数参数 uint num1 与 uint num2，返回值 uint result 都存储在memory中。

### 2.3 **规则3 – 局部变量**

值类型的局部变量存储在内存中。但是，对于引用类型的局部变量，需要显式地指定数据位置。

```
Solidity 
// SPDX-License-Identifier: MIT 
pragma solidity ^0.8.0;
contract Locations {   
/* 此处都是状态变量 */  
// 存储在storage中 
bool flag;   
uint number;   
address account;   
function doSomething() public pure  {  
/* 此处都是局部变量  */  
// 值类型  
// 所以它们被存储在内存中
bool flag2;  
uint number2;   
address account2;   
// 引用类型，需要显示指定数据位置，此处指定为内存 
uint[] memory localArray;    
}  
}  
```

不能显式覆盖具有值类型的局部变量。

```
Solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
function doSomething() public  {  
/* 此处都是局部变量  */   
// 值类型 
bool memory flag2;  
// 错误 
uint Storage number2; 
// 错误 
address account2;  
}  
```



### 2.4 **规则4 – 外部函数的参数**

外部函数的参数(不包括返回参数)存储在 calldata中。