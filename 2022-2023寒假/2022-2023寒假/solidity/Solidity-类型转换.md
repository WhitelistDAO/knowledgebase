# **Solidity-类型转换**

Solidity允许类型之间进行隐式转换和显式转换。

## 1. **隐式转换**

隐式转换时必须符合一定条件，不能导致信息丢失。例如，uint8可以转换为uint16，但是int8不可以转换为uint256，因为int8可以包含uint256中不允许的负值。

## 2. **显式转换**

1. 可以使用构造函数语法，显式地将数据类型转换为另一种类型。

```
Solidity 
int8 y = -3;
uint x = uint(y); 
//Now x = 0xfffff..fd == 在256bit长度的格式下，-3的补码表示
```



1. 转换成更小的类型，会丢失高位。

```
Solidity 
uint32 a = 0x12345678;
uint16 b = uint16(a); // b = 0x5678
```



1. 转换成更大的类型，将向左侧添加填充位。

```
Solidity 
uint16 a = 0x1234;
uint32 b = uint32(a); // b = 0x00001234 
```



1. 转换到更小的字节类型，会丢失后面数据。

```
Solidity
bytes2 a = 0x1234; 
bytes1 b = bytes1(a); // b = 0x12
```



1. 转换为更大的字节类型时，向右添加填充位。

```
Solidity
bytes2 a = 0x1234;
bytes4 b = bytes4(a); // b = 0x12340000
```



1. 只有当字节类型和int类型大小相同时，才可以进行转换。

```
Solidity
bytes2 a = 0x1234; 
uint32 b = uint16(a); // b = 0x00001234 
uint32 c = uint32(bytes4(a)); // c = 0x12340000 
uint8 d = uint8(uint16(a)); // d = 0x34 uint8 e = uint8(bytes1(a)); // e = 0x12
```



1. 把整数赋值给整型时，不能超出范围而发生截断，否则会报错。

```
Solidity 
uint8 a = 12; // no error 
uint32 b = 1234; // no error 
uint16 c = 0x123456; // error, 有截断，变为 0x3456
```

 