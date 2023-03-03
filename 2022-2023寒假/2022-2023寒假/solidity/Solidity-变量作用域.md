# **Solidity-变量作用域**

   局部变量的作用域仅限于定义它们的函数，但是状态变量可以有三种作用域类型：

• **public** – 公共状态变量可以在内部访问，也可以从外部访问。对于公共状态变量，将自动生成一个 getter 函数。

• **private** – 私有状态变量只能从当前合约内部访问，派生合约内不能访问。

• **internal** – 内部状态变量只能从当前合约或其派生合约内访问。

```
Solidity 
// SPDX-License-Identifier: MIT 
pragma solidity ^0.8.0; 
contract C { 
uint public data = 30;  
uint internal iData= 20;   
uint private pData= 10;  
function x() public returns (uint) {   
data = 3; // 内部访问 
iData = 2; // 内部访问    
pData = 1; // 内部访问  
return data;  
}
} 
// 调用外部合约 
contract Caller { 
C c = new C();  
function f() public view returns (uint) {   
return c.data(); // 外部访问  // 
return c.iData(); // error 不允许外部访问   // 
return c.pData(); // error 不允许外部访问  
} 
} 
// 派生合约 
contract D is C { 
uint storedData; // 状态变量  
function y() public returns (uint) {  
data = 3; // 派生合约内部访问  
iData = 2; // 派生合约内部访问   
// pData = 1; //error 不允许派生合约内部访问   
return iData;  
}   
function getResult() public view returns(uint){   
return storedData; // 访问状态变量  
} 
}
```

**注意：**

**1.如果不为状态变量指定访问权限,默认为internal。**

**2.不能使用external修饰状态变量，否则编译时会报错。**

 