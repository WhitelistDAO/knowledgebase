

## React-Hook学习笔记

### Motivation

- Hook使无需在修改组件结构的情况下复用状态逻辑
- Hook将组件中相互关联的部分拆分成更小的函数
- Hook降低了学习成本，在使用非class的情况下使用更多的React特性

### Hooks 

#### useState

```javascript
const [state, setState] = useState('initialState')
```

1. state：当前的 initialState

2. setState：修改state的函数
3. initialState：可以是number string **object **...   
4. state只在组件**首次渲染 **时被创建，下一次重新渲染时，useState返回给我们当前的state

5. 示例：

```jsx
import React, { useState } from 'react';

function Example() {
    const [count, setCount] = useState(0);
    
    return (
        <diV>
        	<p>you clicked {count} times</p>
            <button onClick = {() => {setCount(count+1)}}>
            Click me
            </button>
        </diV>
    )
}
```

#### useEffect

1. 作用：在函数组件中执行副作用操作
   * 修改React组件中的DOM都属于副作用，会触发useEffect函数
2. 无需清除的effect
   + **在 React 更新 DOM 之后运行一些额外的代码。**比如发送网络请求，手动变更 DOM，记录日志，这些都是常见的无需清除的操作。因为我们在执行完这些操作之后，就可以忽略他们了
   + useEffect放在组件内部可以轻松访问state变量或者props

```javascript
useEffect(()=> {
    
}) // 创建或更新
```

```jsx
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

3. 只执行一次

```javascript
useEffect(()=>{

},[])  //仅在组件挂载和卸载时执行
```

4. 依赖参数变化执行

```javascript
useEffect(()=>{

},[A])  //依赖A的变化而执行
```

#### useReducer

1. 作用
   1. useState的替代方法：state逻辑复杂包含多个子值，或者下一个state依赖之前的state
   2. useReducer给触发深更新的组件做性能优化，向子组件传递dispatch而不是回调函数
2. 语法

```javascript
const [state, dispatch] = useReducer(reducer, initialState) //reducer 是一个函数
```

3. 示例

```jsx
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

### 实战训练

1. 动态绑定输入框
2. axios请求

+ Email组件

```jsx
import Address from "./Address"
import Content from "./Content"
import '../../styles/email.css'
import { useState } from "react"
import axios from "axios"

export default function Email() {
    const [email, setEmail] = useState({
        address: '',
        content: ''
    })

    const updateEmail = e => {
        setEmail({
            ...email,
            [e.target.name]: e.target.value
        })
    }

    const submit = () => {
        let reg = /^[a-zA-Z0-9]+([-_.][A-Za-zd]+)*@([a-zA-Z0-9]+[-.])+[A-Za-zd]{2,5}$/
        if (!reg.test(email.address)) {
            alert("邮箱格式不正确")
            setEmail({
                ...email,
                address: "",
                content: email.content
            })
            return
        }
        axios({
            method: "post",
            url: "http://127.0.0.1:8080/user/email",
            data: {
                address: email.address,
                content: email.content
            }
        }).then(res => {
            if (res.data.code === 1) {
                alert("提交成功")
                setEmail({
                    ...email,
                    address: "",
                    content: ""
                })
            } else {
                alert(`提交失败:${res.data.data}`)
            }

        }).catch(err => {
            alert(`提交失败,未知错误:${err}`)
        })
    }

    return <div className="email">
        <Address addressChange={updateEmail} data={email.address} />
        <Content contentChange={updateEmail} content={email.content} />
        <div className="submit">
            <button onClick={submit}>提交</button>
        </div>
    </div>
}
```

+ Address组件

```jsx

export default function Address(props) {

    return <div className="address">
        {/* <label>email</label> */}
        <input name="address" value={props.data} placeholder="请输入邮件地址" onChange={props.addressChange} type="text">
        </input>
    </div>
}



```

+ Content组件

```jsx
export default function Content(props) {
    return <div className="content">
        {/* <label >content</label> */}
        <textarea name="content" value={props.content} placeholder="请填写邮件内容" row='5' cols='33' onChange={props.contentChange}>

        </textarea>
    </div>
}
```









