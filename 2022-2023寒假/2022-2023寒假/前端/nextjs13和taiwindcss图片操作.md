## 图片类操作
通用方法：

```js
        <Image layout="fill" objectFit="contain"/> // 宽度铺满整个容器，高度等比缩放，超出则裁剪
        <Image layout="fill" objectFit="cover"/> // 高度铺满，宽度等比缩放，超出则裁剪
```
图片一般放在div内，一般是relative定位。但是需要指定div的w h(w和h最好用px否则可能会显示不出来)，否则image不会显示 p填充也是内容。div中可以放背景颜色，背景一般可以使用渐变。 contain先满足width，cover先满足height。

nextjs13 支持style属性，用fill属性代替以前的layout=“fill”。 13版本尽量用width和height属性，里面代表px。控制父元素div的大小和图片的大小，可以制作出卡片。如果父元素没得大小，图片有wh属性值，还是可以显示出图像。因为图片是div的内容
```js
<div style={{position:"relative"}}>
  <Image
      src={source}
      alt=""
      fill
      style={{objectFit:"cover"}}
    />
</div>
```

渐变操作示例: bg-gradient-to-r (方向) from-cyan-500(从什么到什么颜色) to-teal-500
```js
<a className="bg-gradient-to-r from-cyan-500  to-teal-500 text-white px-4 py-2 border-none rounded-md ml-8" href="#">
        Resume
</a>
```
简单card制作：
1. flex-1: flex item grow and shrink as needed, ignoring its initial size.
2. flex-initial : flex item shrink but not grow, taking into account its initial size.
3. flex-auto: flex item grow and shrink, taking into account its initial size. 
4. Use `flex-none` to prevent a flex item from growing or shrinking:
```js
<div className="text-center shadow-lg p-10 rounded-xl my-10 flex-1 dark:bg-white ">
        <Image
          src={'/test1.png'}
          width={100}
          height={100}
          alt={''}
          className="mx-auto"
        />
        <h3 className="text-lg font-medium pt-8 pb-2  ">Beautiful Designs</h3>
        <p className="py-2">
          Creating elegant designs suited for your needs following core design
          theory.
        </p>
</div>
```
fill是不知道大小的时候使用，与wh属性不兼容。这样还可以设计md 或者 lg时候的图片需要手动调整一下p的值
```js
<div className="relative h-[350px] w-[300px] text-center shadow-slate-400 shadow-md">
          <Image
            src={'/test1.png'}
            width={256}
            height={256}
            className="mx-auto mt-4"
            alt={''}
          />
          <h1 className="text-3xl text-teal-400 pt-3">hello</h1>
          <p>click me</p>
        </div>
```