# Vue3学习

Vue核心dom和虚拟算法

虚拟dom是通过js生成的AST节点树

- 为什么要有虚拟dom一个dom上面的属性非常多，直接操作dom非常浪费性能用js计算性能换取操作dom的性能，尽可能减少dom操作

- 介绍diff算法
  
  数组的splice函数：三个参数
  
  ---
  
  index必须。整数，指定在上面位置添加/删除项目，使用负值指定从数组末尾开始的位置

howmany 可选。要删除的项目数。如果设置为0，则不会删除任何项目

item1,...,itemx 可选。要添加到数组中的新项目

- 有key
1. 前序对比算法

2. 后序对比算法

3. 新节点如果多出来，就是挂载通过patch函数第一个参数为null

4. 旧节点如果多出来就是卸载

5. 特殊情况乱序
- 无key

无key patch的时候会替换

- 乱序
1. 尽可能的复用

# Ref学习

##### ref

1. ref 接收一个内部值 返回一个 响应式且可变的对象。ref对象仅有一个 .value peoperty 。指向该内部值

2. script里面 要用ref.value 来赋值，但展示在页面上可以不用 .value。vue模板会自动解包

3. Ref ts对应的接口; 注意ref包装之后需要 .value 来进行赋值

4. 声明的时候可以不用申明类型，ref可以自己推断类型
   
   ```
    interface Ref<T> {
        value: T
    }
   ```
   
   ##### isRef
   
   判断对象是不是一个ref对象;
   
   ```let msg: Ref<string | number> = ref("我是message")console.log(isRef(msg))   // true```
   let msg: Ref&lt;string | number&gt; = ref(&quot;我是message&quot;)
   console.log(isRef(msg)) // true
   
   ```
   
   ##### shallowRef
   
   创建一个跟踪自身.value的ref，但不会使其值变成响应式的。 响应式是可以响应视图
   
   ```
   
   const foo = shallowRef({}) // 非响应式
   // 改变 ref 的值是响应式的
   foo.value = {} // 但是这个值不会被转换。
   isReactive(foo.value) // false
   
   ```
   
   ##### triggerRef
   
   使用triggRef后会强制更新视图
   
   ```
   
   const shallow = shallowRef({
    greet: 'Hello, world'})
   
   // 第一次运行时记录一次 "Hello, world"
   watchEffect(() => { 
   console.log(shallow.value.greet)
   })
   
   // 这不会触发副作用，因为 ref 是浅层的
   shallow.value.greet = 'Hello, universe'
   
   // 记录 "Hello, universe" //强制更新视图
   triggerRef(shallow)
   
   ```
   
   ##### customRef
   
   自定义ref，customRef是一个工厂函数要求我们返回一个对象 并且实现get set 适合去做防抖
   
   ```java
   function myRef<T = any>(value: T) { 
   let timer:any; 
   return customRef((track, trigger) => { 
   return { 
   get() { 
   track() 
   return value 
   },
    set(newVal) {
    clearTimeout(timer)
    timer = setTimeout(() => { 
   console.log('触发了set') 
   value = newVal 
   trigger() 
   },500) 
   }
    }
    })
   }
   ```
