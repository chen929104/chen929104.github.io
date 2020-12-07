---
layout: posts
title: useEffect详解
date: 2020-11-23 22:07:09
tags:
---

##### 介绍

react 在 16.8 版本中引入了新特新 Hooks,在使用 Hooks 的过程中会有一些有趣的事情,有时候你也可以去自定义 Hooks 来抽出一些重复的逻辑。在学习使用 Hooks 的时候难免会遇到一些问题。故写这篇文章来加深对 useEffect 的理解。
useEffect 是一个重要的 Hooks,在使用的过程中很像类组件中的 componentDidMount,componentWillUnmount 的结合。

##### 类组(Class Components)件和函数组件(Function Components)

在以前使用类组件的时候习惯了 react 的生命周期组件的 mount，updata,unmount 我们可以以此来定义一个类组件，并在组件加载完成后又一个定时器在组件销毁是销毁定时器

```
class Demo extends React.Component{
   constructor(props) {
    super(props);
    this.state = {};
  }

  componentDidMount() {
     this.timerID = setInterval(
      () => {},
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }
  render(){

  }
}
```

函数组件在使用过程中却并没有生命周期,故而引用了 Hooks 来替代生命周期,一个简单的函数组件并实现上述功能

```
function Demo{
  useEffect(()=>{
   const timerID = setInterval(
      () => {},
      1000
    );
    return  clearInterval(timerID);
  })
  return ....
}
```

回归主题，来讲一下 useEffect

---

##### react 的渲染

在使用过程中，遇到过修改了 stast 或者 props 时 useEffect 没有获取到最新的状态出现了许多的问题，发现 react 渲染时会直接重新执行 render

```
const Demo = （）=>{
  const [count,setCount] = useState(0);

  return <div>
    <div>{count}</div>
    <Button onClick={()=>{setCount(count+1)}} >onClick</Button>
  <div>
}
```

上述代码在 react 的执行中并没有进行双向绑定，没有监视其改变，count 仅仅像一个普通的数字在刷新的时候进行替换使用新的数字进行组件的重新渲染，然后重新更新 dom

```
// 初始
const Demo = （）=>{
  const count  = 0

  return <div>
    <div>{count}</div>
    <Button onClick={()=>{setCount(count+1)}} >onClick</Button>
  <div>
}

// 点击一次
const Demo = （）=>{
  const count  = 1

  return <div>
    <div>{count}</div>
    <Button onClick={()=>{setCount(count+1)}} >onClick</Button>
  <div>
}

// 点击两次
const Demo = （）=>{
  const count  = 2

  return <div>
    <div>{count}</div>
    <Button onClick={()=>{setCount(count+1)}} >onClick</Button>
  <div>
}
```

所以每一次状态更新的时候 state 和 props 是会重新生产一个新的状态执行 render，上一个 count 和当前的 count 没有关系是独立与其他渲染的

这个处理方式在 function 中也有着体现

```
const Demo = （）=>{
  const [count,setCount] = useState(0);
  const alertCount  = ()=>{
    setTimeout(()=>{
      alert(count);
    },3000)
  }
  return <div>
    <div>{count}</div>
    <button onClick={()=>{setCount(count+1)}} >onClick</button>
    <button onClick={alertCount} >alertCount</button>
  <div>
}
```

当我们点击了 alertCount 并快速且多次的点击 onClick，react alert 出的 count 会是 0，因为 react 的渲染问题每次的 count 都是一个新的状态在每一次 render 是一个常数并赋值给了 function 相当于在点击的时候点击了

```
  function alertFunction = ()=>{
    setTimeout(()=>{
      alert(0)
    },3000)
  }
```

当你想要获取到最新的 state 时，你可以使用 useRef 去保存数据

##### 每次渲染也有自己的 effect

正如我们所说每次渲染时都会有一个独立的 state 那么对于 effect 呢

```
function Counter() {
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

正如上面那个例子，每一次许渲染的时候都会生产一个 effect 获取到一个常数 count 像下面的代码

```
  const count = 0

  useEffect(() => {
    document.title = `You clicked 0 times`;
  });
```

---

未完待续
