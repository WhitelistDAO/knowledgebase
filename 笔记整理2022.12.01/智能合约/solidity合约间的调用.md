# solidity合约间的调用-何杰安

在中大型的项目中，我们不可能在一个智能合约中实现所有的功能，而且这样也不利于分工合作。一般情况下，我们会把代码按功能划分到不同的库或者合约中，然后提供接口互相调用。

##### 一、隐式转换地址为合约

    // SPDX-License-Identifier: GPL-3.0-or-laterpragma solidity ^0.7.6;contract Caller {    int public x;    uint public y;    address public addr;        function inc_call(Callee _callee,uint256 setCalleey) public {        _callee.inc(setCalleey);    }}contract Callee {    int public x;    uint public y;    address public addr;    function inc(uint256 a) public {        x++;        addr = msg.sender;        y = a;    }}

我们先部署两个合约Callee和Caller，然后在合约Caller合约中调用Callee合约中的inc函数（需要在Caller合约的inc_call函数中输入Callee合约的地址），并查看调用合约后两个合约的状态变量的变化。

合约初始状态：

Callee合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=NDA5YzUzMjY5MzMxZDcwYmZmOGUwNDk5NzkwNTQ2ZjJfZWdOOG9rVFc3TEZpOHNGQ3RzRHBZT0tzd2IwZVRNRWJfVG9rZW46Ym94Y25hMmVLeVVLZ1J2UXFHWk41bjZBakhiXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

Caller合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=NjZlZDBhNmU1ZjMzZGRjYWIzMDZlZWQxNzUzZjkwMGJfMWdleldlaWtHNDBJbWsxU1V3Y1NCaEdTNFlPVTBGYU9fVG9rZW46Ym94Y24yRzZOV3c4S0kwbGszVTFxNVRrUDZlXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

调用后合约状态：

Callee合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=MGUxYjk3MWNiODEzOTgzZmU3MGE0ZDhkNGM4MjU3YTVfR29JY2hTTjI3WWtRTTJoZzc0aXNwb0hrU24yMGNWcXZfVG9rZW46Ym94Y24wSkVBZDROR0lXNHRXckJINklOZHQzXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

Caller合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=MzgyYTRiOTIxOGU4ZDMyZTRjYmRiZWI3YWIyMmIxMDhfMTVhelZhMzVNSTh1c2RkQWhvMjlBVkFzNlQ0Vlg2cjhfVG9rZW46Ym94Y240WWlHMHFMVmZueWY1V3VsOXZ6U3BkXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

可以看到Callee合约中的状态变量发生了改变，而Caller合约中的状态变量没有改变。

##### 二、显式转换地址为合约

    // SPDX-License-Identifier: GPL-3.0-or-laterpragma solidity ^0.7.6;contract Caller {    int public x;    uint public y;    address public addr;        function inc_call(address addr,uint256 setCalleey) public {        Callee(addr).inc(setCalleey);    }}contract Callee {    int public x;    uint public y;    address public addr;    function inc(uint256 a) public {        x++;        addr = msg.sender;        y = a;    }}

方法二和方法一的调用结果一致。

##### 三、使用call

    // SPDX-License-Identifier: GPL-3.0-or-laterpragma solidity ^0.7.6;contract Caller {    int public x;    uint public y;    address public addr;        function inc_call(address _addr,uint256 setCalleey) public {        _addr.call(abi.encodeWithSignature("inc(uint256)",setCalleey));    }}contract Callee {    int public x;    uint public y;    address public addr;    function inc(uint256 a) public {        x++;        addr = msg.sender;        y = a;    }}

方法三与方法一调用结果一致

##### 四、使用callcode

    // SPDX-License-Identifier: GPL-3.0-or-laterpragma solidity ^0.4.26;contract Caller {    int public x;    uint public y;    address public addr;        function inc_callcode(address _addr,uint256 setCallery) public {        _addr.callcode(abi.encodeWithSignature("inc(uint256)",setCallery));    }}contract Callee {    int public x;    uint public y;    address public addr;    function inc(uint256 a) public {        x++;        addr = msg.sender;        y = a;    }}

合约初始状态：

Callee合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=MjcxYzhlNmQwZTc2MDYwOTYwZmFjNjU4NDZmMjBjNWNfTXJYRVdLaTAzY20zOU9UU1NEZU1IU2Z6cWhwOXdVbWhfVG9rZW46Ym94Y25PNkFwcjlYMUZVd0Z5bkpxUDRPYVBjXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

Caller合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=MzdmZmUxOTgwY2M1YzgxZjEzMDYwY2U2YTAwYzJkNjZfN2I5QUxCVFQ1ZjJhTmRnVVo0bjFaeWZHdGQ3dTdvRklfVG9rZW46Ym94Y25IRlZOZVVXTVNOc2xpSkhXcEQ5TTZjXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

调用后合约状态：

Callee合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=OTAyNzc3MDVlNGNjMjM1ODM2ZWQwN2ZiYzEzMWI4YzlfMWNESmVoTklqUDRvVFJDWE9qUHJUUXNSVmhNTWpRaFhfVG9rZW46Ym94Y25rRHNDa3pWSnE1OWx4RWV3bzRrYlNmXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

Caller合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=ZWJiNGI4ZWE1NGI0OTJmMDZjOTFmZTk3ODM2YmM3OGRfd2FMenZ4dFpUMnhTdGJSU1NsWWhrVEZYTmFDVm95NmdfVG9rZW46Ym94Y240UVFYaWpQOHNSampYRVk5N05Sak9nXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

可以看到Callee合约没有变化，Caller合约发生了变化。

##### 五、使用delegatecall

    // SPDX-License-Identifier: GPL-3.0-or-laterpragma solidity ^0.7.6;contract Caller {    int public x;    uint public y;    address public addr;        function inc_delegatecall(address _addr,uint256 setCallery) public {        _addr.delegatecall(abi.encodeWithSignature("inc(uint256)",setCallery));    }}contract Callee {    int public x;    uint public y;    address public addr;    function inc(uint256 a) public {        x++;        addr = msg.sender;        y = a;    }}

合约初始状态：

Callee合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=OGY0YTE5YmNkNzQ3YTQ4MjkxOWI5NjM3ZjY2OWYzMjVfcm9HSG5RV3Zqb00zYlZrWmlEc0lnWG41TnZZa2lGOTBfVG9rZW46Ym94Y25FUlBYMGxHb1NQZURjcFRmcThMeXVlXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

Caller合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2Q4NWM3Mzk4YmU2M2IwMmU2MzY4MGYxMjJiMjk1MTZfY3QySktyZFNBNFkxTTBHc2c3OWE4YlFyWFJVRDA3RVpfVG9rZW46Ym94Y25jalBleUdndmQzYnJaWnJBc3NGa0pjXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

调用后合约状态：

Callee合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=NGQxOGI0ODlmMjNhODMyMzllMzM1YTllMDQ1YmVjOTRfVzg3RTQxZE5VcjhOcm1nTTlkdjRPbnVnVmhTRXpsNHlfVG9rZW46Ym94Y25kdTZoYWd6eE9VN0h0Tkc0Sk9aV1NkXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

Caller合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=ZmFiM2NiZGJiNzk4OGU2YjBjMWY2Y2Y1YmY2YTAwMWZfUjV2MWZ1OERuSXlXUjJYQ2hma0ozUER3eEUzamVYS3JfVG9rZW46Ym94Y25Bazh5aGd3eEVZVEhrTDFJeXFpOWFmXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

可以看到Callee合约没有发生变化，Caller合约发生了变化，并且状态变量addr与前面几个方法的addr都不一样，前几个方法的addr为Caller合约的地址，而这里的addr为外部账户的地址。

##### 六、调用另一个合约的状态变量

当我们想访问另一个合约的状态变量时，应该如何操作呢？其实每一个public修饰的状态变量都会有一个getter函数，以便外部去查询该状态变量的值。废话不多说，我们直接看代码。

    // SPDX-License-Identifier: GPL-3.0-or-laterpragma solidity ^0.7.6;contract Caller {    uint public y;        function sety(address addr) public {        y = Callee(addr).y();    }}contract Callee {        uint public y = 3;}

合约初始状态：

Callee合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=OWNmN2FhYThiMjk2NDlhYTIwZTczYzA3YzY0OTYwYzZfSXAzTXlMQWNUNUNpcFdDUlFwRDBTMmwyN09kZ05LNE5fVG9rZW46Ym94Y25Oa0xWdW1iSkJISE81WnJjYWdNZGJOXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

Caller合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=OTJhMDZiMjRkOWMxNmRiMTBlMzJmYTVlOTE5NTdjOGNfMkhaWmt4MmVuTTdKU0VNektSRkxVU0syWmlNRFNVbXJfVG9rZW46Ym94Y25PZWZXN25ZZ0xzNTlhVVBnWDhha3I0XzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

调用后合约状态：

Caller：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=MDZhYjc3MWQ0YTQ4MGI4ZmNjM2U0MTc2YTcwY2E2ZjhfSWJSNXBvTXhyN1dKTjM4VVh5YWdhRm00QzE2ZW5hQUZfVG9rZW46Ym94Y25ydkZ5VGg3aHFIc3dldjh6dEZ0MXBiXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

我们调用Caller合约中的sety函数，使得Caller合约中的状态变量y等于Callee合约中的状态变量y。

##### 七、调用另一个合约的internal或private函数

我们都知道，private函数只能在合约内部调用，internal函数只能在合约内或者继承的合约内调用。那有没有可能让另一个非继承的合约调用一个合约内的private函数或internal函数呢？答案是可以的，我们既然在外部调用不了internal和private函数，那我们可以调用该合约的回退函数，再让回退函数去调用internal和private函数。废话不多说，直接上代码。

    // SPDX-License-Identifier: GPL-3.0-or-laterpragma solidity ^0.7.6;contract Caller {        function inc_private(address _addr,uint256 setCallery) public {        _addr.call(abi.encodeWithSignature("inc_private(uint256)",setCallery));    }    function inc_internal(address _addr) public {        _addr.call(abi.encodeWithSignature("inc_internal()"));    }}contract Callee {    int public x;    uint public y;    address public addr;    fallback() external{        if(msg.sig == bytes4(keccak256("inc_private(uint256)"))){            uint a = toUint256(msg.data,4);            inc_private(a);        }else if(msg.sig == bytes4(keccak256("inc_internal()"))){            inc_internal();        }    }        function inc_private(uint256 a) private {        addr = msg.sender;        y = a;    }    function inc_internal() internal {        x++;    }    function toUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256) {    require(_bytes.length >= (_start + 32), "Read out of bounds");    uint256 tempUint;    assembly {        tempUint := mload(add(add(_bytes, 0x20), _start))    }    return tempUint;    }}

Callee合约初始状态：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=ZmM4Yzk5NDdmZTllYmEzMmY3NTU1YzM3MzhkZTBlMTNfYlMyOWVzQ2JIOENlWDk0QlRXcDZsb2pBOU01MDdJdjdfVG9rZW46Ym94Y243V3BvQm40UkpVdllMd3hVU0E2ZVljXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

调用后合约状态：

Callee合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=MTcyMjI0NjFkMTU4YTI3ZWRmMTQ3MDI3NzVhY2ZjNzdfM3dBOVZGajZ2SUJHaFZjYVZsTXlRY3p4a1BJZXVGcUpfVG9rZW46Ym94Y25Ca3NmUGIxSDUySGRYTEF2R0lUYjhmXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

Caller合约：

![](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=ZmU1YjNiMWYwMDhmNGJlNGQxMDU4YzM0ZDkxZDUxNDBfVTVpellSOXhWMUJlY01VdHFxc3hkZDQyTXRaOU5CYjBfVG9rZW46Ym94Y25xb3A1N0dFQ3A0anNoaVBBUmx1VmJkXzE2NzA0MDA3ODQ6MTY3MDQwNDM4NF9WNA)

可以看到Callee合约的状态变量发生了相应的变化，说明我们成功在Caller合约中调用Callee合约中的internal和private函数。

在Callee合约的回退函数中，我们使用toUint256函数来获取msg.data中传过来的参数。msg.data中的前4个字节为函数选择器，表示你要调用的函数；msg.data中第4个字节之后每32个字节为一个参数。

##### 八、总结

1. 方法一、二、三改变的是被调用合约Callee的状态，而方法四、五改变的是调用合约Caller的状态。

2. 方法一、二、三、四的调用者地址msg.sender为调用合约Caller的合约地址，而方法五的调用者地址msg.sender为外部账户的地址。
