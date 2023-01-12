# solidity

## 基础数据类型

**boolean**

**uint**：是无符号的整数，表示这个数字不是可正可负的，只能是正数

​           可以规定大小，uint8表示分配了8bit，当然，默认的话是

​           uint256。

**int**：表示正数或负数

**address**：表示地址

**bytes**

**string**

## 权限

**public：**public 会创建storage和state变量的getter函数

**private：**表示只有这个合约可以调用这个函数，对于storage来说，它不是说只有这个合约才能读取它的值。只对合约内部可见。

**external：**对合约外部可见，表示合约外账户可以调用这个函数。

**internal：**表示只有这个合约或者继承它的合约可以读取

## 函数

函数通过function关键字表示

<u>创建一个名字叫做“store”的函数</u>

`function store（uint256  num）public{`

`}`

###  关键字view和pure

被表示的函数调用时不需要消耗gas

如果一个函数是view函数，意味着我们只会读取这个合约状态，view函数不允许修改任何状态

pure函数也不允许修改状态

如果一个要改变区块链状态的函数



