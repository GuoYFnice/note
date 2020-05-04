# react

## 基本内容

```react
import React, { Component } from 'react'
import ReactDOM from 'react-dom'
import './index.css'

class Header extends Component {
  render () {
    return (
      <div>
        <h1>React 小书</h1>
      </div>
    )
  }
}

ReactDOM.render(
  <Header />,
  document.getElementById('root')
)
```

### ReactDOM

`ReactDOM` 可以帮助我们把 React 组件渲染到页面上去

ReactDOM.render` 功能就是把组件渲染并且构造 DOM 树，然后插入到页面上某个特定的元素上（在这里是 id 为 `root` 的 `div` 元素）。`

只要你要写 React.js 组件，那么就必须要引入这两个东西(React/组件父类component)



### JSX

***所谓的 JSX 其实就是 JavaScript 对象***

**JSX到页面的过程：JSX先通过babel编译和react.js构造成为JavaScript对象在通过reactdom.render变成dom元素，最后插入页面**

因为class是JavaScript的关键字，所以使用className来进行增加类名

***自定义的组件都必须要用大写字母开头，普通的 HTML 标签都用小写字母开头***

 *on\* 的事件监听只能用在普通的 HTML 的标签上，而不能用在组件标签上*



### setState

`setState` 方法由父类 `Component` 所提供。*当我们调用这个函数的时候，React.js 会更新组件的状态 state ，并且重新调用 render 方法，然后再把 render 方法所渲染的最新的内容显示到页面上*。

**当调用setState是，react.js不会立刻修改state，而是把这个对象放到一个更新队列里面，稍后才会从队列当中把新的状态提取出来合并到 `state` 当中，然后再触发组件更新。**

例如：

```javascript
...
  handleClickOnLikeButton () {
    this.setState({ count: 0 }) // => this.state.count 还是 undefined
    this.setState({ count: this.state.count + 1}) // => undefined + 1 = NaN
    this.setState({ count: this.state.count + 2}) // => NaN + 2 = NaN
  }
...
```

上面的代码的运行结果并不能达到我们的预期，我们希望 `count` 运行结果是 `3` ，可是最后得到的是 `NaN`。但是这种后续操作依赖前一个 `setState` 的结果的情况并不罕见。

这里就自然地引出了 `setState` 的第二种使用方式，可以接受一个函数作为参数。React.js 会把上一个 `setState` 的结果传入这个函数，你就可以使用该结果进行运算、操作，然后返回一个对象作为更新 `state` 的对象：

```javascript
...
  handleClickOnLikeButton () {
    this.setState((prevState) => {
      return { count: 0 }
    })
    this.setState((prevState) => {
      return { count: prevState.count + 1 } // 上一个 setState 的返回是 count 为 0，当前返回 1
    })
    this.setState((prevState) => {
      return { count: prevState.count + 2 } // 上一个 setState 的返回是 count 为 1，当前返回 3
    })
    // 最后的结果是 this.state.count 为 3
  }
```

setstate可以接受两种格式（对象和函数）

**对象：传入一个对象的时候，这个对象表示该组件的新状态。但你只需要传入需要更新的部分就可以了，而不需要传入整个对象**

**函数：可以接受一个函数作为参数。React.js 会把上一个 `setState` 的结果传入这个函数**

渲染多个setstate时不用担心性能，因为React.js 内部会把 JavaScript 事件循环中的消息队列的同一个消息中的 `setState` 都进行合并以后再重新渲染组件

1. **setState只在合成事件和钩子函数中是“异步”的，在原生事件和setTimeout 中都是同步的。**
2. **setState 的“异步”并不是说内部由异步代码实现，其实本身执行的过程和代码都是同步的，只是合成事件和钩子函数的调用顺序在更新之前，导致在合成事件和钩子函数中没法立马拿到更新后的值，形成了所谓的“异步”，当然可以通过第二个参数 setState(partialState, callback) 中的callback拿到更新后的结果。**
3. **setState 的批量更新优化也是建立在“异步”（合成事件、钩子函数）之上的，在原生事件和setTimeout 中不会批量更新，在“异步”中如果对同一个值进行多次setState，setState的批量更新策略会对其进行覆盖，取最后一次的执行，如果是同时setState多个不同的值，在更新时会对其进行合并批量更新。**

### props

1. 为了使得组件的可定制性更强，在使用组件的时候，可以在标签上加属性来传入配置参数。
2. 组件可以在内部通过 `this.props` 获取到配置参数，组件可以根据 `props` 的不同来确定自己的显示形态，达到可配置的效果。
3. 可以通过给组件添加类属性 `defaultProps` 来配置默认参数。
4. `props` 一旦传入，你就不可以在组件内部对它进行修改。但是你可以通过父组件主动重新渲染的方式来传入新的 `props`，从而达到更新的效果。



**dangerouslySetHTML**

为了预防xss攻击，在 React.js 当中所有的表达式插入的内容都会被自动转义

需要给 `dangerouslySetInnerHTML` 传入一个对象，这个对象的 `__html` 属性值就相当于元素的 `innerHTML`，这样我们就可以动态渲染元素的 `innerHTML` 结构了。



## 生命周期

React 16.8 +的生命周期分为三个阶段,分别是挂载阶段、更新阶段、卸载阶段

### 挂载阶段

 *React.js 将组件渲染，并且构造 DOM 元素然后塞入页面的过程称为组件的挂载*

```
-> constructor()
-> componentWillMount()
-> render()
// 然后构造 DOM 元素插入页面
-> componentDidMount()
// 即将从页面中删除
-> componentWillUnmount()
// 从页面中删除
```

`componentWillMount` 和 `componentDidMount` 都是可以像 `render` 方法一样自定义在组件的内部。挂载的时候，React.js 会在组件的 `render` 之前调用 `componentWillMount`，在 DOM 元素塞入页面以后调用 `componentDidMount`。



**我们一般会把组件的 `state` 的初始化工作放在 `constructor` 里面去做；在 `componentWillMount` 进行组件的启动工作，例如 Ajax 数据拉取、定时器的启动；组件从页面上销毁的时候，有时候需要一些数据的清理，例如定时器的清理，就会放在 `componentWillUnmount` 里面去做。**

**说一下本节没有提到的 `componentDidMount` 。一般来说，有些组件的启动工作是依赖 DOM 的，例如动画的启动，而 `componentWillMount` 的时候组件还没挂载完成，所以没法进行这些启动工作，这时候就可以把这些操作放在 `componentDidMount` 当中。**



### 更新阶段

更新阶段是`setState` 导致 React.js 重新渲染组件并且把组件的变化应用到 DOM 元素上的过程，*这是一个组件的变化过程*

1. `shouldComponentUpdate(nextProps, nextState)`：你可以通过这个方法控制组件是否重新渲染。如果返回 `false` 组件就不会重新渲染。这个生命周期在 React.js 性能优化上非常有用。
2. `componentWillReceiveProps(nextProps)`：组件从父组件接收到新的 `props` 之前调用。
3. `componentWillUpdate()`：组件开始重新渲染之前调用。
4. `componentDidUpdate()`：组件重新渲染并且把更改变更到真实的 DOM 以后调用。

### 卸载阶段

```
即将从页面删除
->componentWillUnmount
从页面删除
```

### 





## 高阶组件

***高阶组件就是一个函数，传给它一个组件，它返回一个新的组件。***

*其实就是为了组件之间的代码复用*。组件可能有着某些相同的逻辑，把这些逻辑抽离出来，放到高阶组件中进行复用。*高阶组件内部的包装组件和被包装组件之间通过 props 传递数据*





## context

组件只要往自己的 context 里面放了某些状态，这个组件之下的所有子组件都直接访问这个状态而不需要通过中间组件的传递。一个组件的 context 只有它的子组件能够访问，它的父组件是不能访问到的，你可以理解每个组件的 context 就是瀑布的源头，只能往下流不能往上飞。

#### 如何使用

一个组件可以通过 `getChildContext` 方法返回一个对象，这个对象就是子树的 context，提供 context 的组件必须提供 `childContextTypes` 作为 context 的声明和验证。如果一个组件设置了 context，那么它的子组件都可以直接访问到里面的内容，它就像这个组件为根的子树的全局变量。任意深度的子组件都可以通过 `contextTypes` 来声明你想要的 context 里面的哪些状态，然后可以通过 `this.context` 访问到那些状态。

context 打破了组件和组件之间通过 `props` 传递数据的规范，极大地增强了组件之间的耦合性。而且，就如全局变量一样，*context 里面的数据能被随意接触就能被随意修改*，每个组件都能够改 context 里面的内容会导致程序的运行不可预料。





## hooks

什么是hooks

> *Hook* 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。

```react
import React, { useState } from 'react';

function Example() {
  // 声明一个新的叫做 “count” 的 state 变量
  const [count, setCount] = useState(0);

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

### state hook

const [count, setCount] = useState(0);

通过解构 [] 对state进行赋值

### effect hook

> *Effect Hook* 可以让你在函数组件中执行副作用操作
>
> 如果你熟悉 React class 的生命周期函数，你可以把 `useEffect` Hook 看做 `componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 这三个函数的组合。











## react组件实例

### DataList（列表）

使用传参控制列表是否为有序

```react
function DataList({ isOrdered , data }){
	const list = data.map((val,i) => <li key={`${i}_${val}`>{val}</li>);
    return isOrdered ? <ol>{list}</ol> : <ul>{list}</ul>
}
```

### DataTable（表格）

通过map对数据进行表格展示

```react
function DataTable({ data }) {
  return (
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Value</th>
        </tr>
      </thead>
      <tbody>
        {data.map((val, i) => (
          <tr key={`${i}_${val}`}>
            <td>{i}</td>
            <td>{val}</td>
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

### MappedTable（带键值的表格）

寻找具有指定键值的table

```react
function MappedTable({ data, propertyNames }) {
  let filteredData = data.map(v =>
    Object.keys(v)
      .filter(k => propertyNames.includes(k))
      .reduce((acc, key) => ((acc[key] = v[key]), acc), {})
  );
  return (
    <table>
      <thead>
        <tr>
          {propertyNames.map(val => (
            <th key={`h_${val}`}>{val}</th>
          ))}
        </tr>
      </thead>
      <tbody>
        {filteredData.map((val, i) => (
          <tr key={`i_${i}`}>
            {propertyNames.map(p => (
              <td key={`i_${i}_${p}`}>{val[p]}</td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```



### click（点击事件）

 用于处理在包装好的组件外部单击的事件 

```react
const useClickOutside = (ref, callback) => {
  const handleClick = e => {
    if (ref.current && !ref.current.contains(e.target)) {
      callback();
    }
  };
  React.useEffect(() => {
    document.addEventListener('click', handleClick);
    return () => {
      document.removeEventListener('click', handleClick);
    };
  });
};
```

 用于处理在包装好的组件内部单击的事件。 

```react
const useClickInside = (ref, callback) => {
  const handleClick = e => {
    if (ref.current && ref.current.contains(e.target)) {
      callback();
    }
  };
  React.useEffect(() => {
    document.addEventListener('click', handleClick);
    return () => {
      document.removeEventListener('click', handleClick);
    };
  });
};
```



### useFatch（网络请求）

处理网络请求

```react
const useFetch = (url, options) => {
  const [response, setResponse] = React.useState(null);
  const [error, setError] = React.useState(null);

  React.useEffect(() => {
    const fetchData = async () => {
      try {
        const res = await fetch(url, options);
        const json = await res.json();
        setResponse(json);
      } catch (error) {
        setError(error);
      }
    };
    fetchData();
  }, []);

  return { response, error };
};
```



### useTimeout（定时器）

 以声明方式实现setTimeout 

```react
const useTimeout = (callback, delay) => {
  const savedCallback = React.useRef();

  React.useEffect(() => {
    savedCallback.current = callback;
  }, [callback]);

  React.useEffect(() => {
    function tick() {
      savedCallback.current();
    }
    if (delay !== null) {
      let id = setTimeout(tick, delay);
      return () => clearTimeout(id);
    }
  }, [delay]);
};
```



### input（输入框）

普通输入框

```react
function ControlledInput({
  callback,
  type = 'text',
  disabled = false,
  readOnly = false,
  defaultValue,
  placeholder = ''
}) {
  const [value, setValue] = React.useState(defaultValue);

  React.useEffect(() => {
    callback(value);
  }, [value]);

  return (
    <input
      defaultValue={defaultValue}
      type={type}
      disabled={disabled}
      readOnly={readOnly}
      placeholder={placeholder}
      onChange={({ target: { value } }) => setValue(value)}
    />
  );
}
```



### LimitedTextarea（受控多行输入）

多行输入

```react
function LimitedTextarea({ rows, cols, value, limit }) {
  const [content, setContent] = React.useState(value);

  const setFormattedContent = text => {
    text.length > limit ? setContent(text.slice(0, limit)) : setContent(text);
  };

  React.useEffect(() => {
    setFormattedContent(content);
  }, []);

  return (
    <div>
      <textarea
        rows={rows}
        cols={cols}
        onChange={event => setFormattedContent(event.target.value)}
        value={content}
      />
      <p>
        {content.length}/{limit}
      </p>
    </div>
  );
}
```



### MultiselectCheckbox（多选框）

多选框

```react
const style = {
  listContainer: {
    listStyle: 'none',
    paddingLeft: 0
  },
  itemStyle: {
    cursor: 'pointer',
    padding: 5
  }
};

function MultiselectCheckbox({ options, onChange }) {
  const [data, setData] = React.useState(options);

  const toggle = item => {
    data.forEach((_, key) => {
      if (data[key].label === item.label) data[key].checked = !item.checked;
    });
    setData([...data]);
    onChange(data);
  };

  return (
    <ul style={style.listContainer}>
      {data.map(item => {
        return (
          <li key={item.label} style={style.itemStyle} onClick={() => toggle(item)}>
            <input readOnly type="checkbox" checked={item.checked || false} />
            {item.label}
          </li>
        );
      })}
    </ul>
  );
}
```



### Select（选择框）

```react
function Select({ values, callback, disabled = false, readonly = false, selected }) {
  return (
    <select
      disabled={disabled}
      readOnly={readonly}
      onChange={({ target: { value } }) => callback(value)}
    >
      {values.map(([value, text]) => (
        <option selected={selected === value} value={value}>
          {text}
        </option>
      ))}
    </select>
  );
}
```



### Accordion（手风琴菜单）

手风琴菜单

```react
function AccordionItem(props) {
  const style = {
    collapsed: {
      display: 'none'
    },
    expanded: {
      display: 'block'
    },
    buttonStyle: {
      display: 'block',
      width: '100%'
    }
  };

  return (
    <div>
      <button style={style.buttonStyle} onClick={() => props.handleClick()}>
        {props.label}
      </button>
      <div
        className="collapse-content"
        style={props.isCollapsed ? style.collapsed : style.expanded}
        aria-expanded={props.isCollapsed}
      >
        {props.children}
      </div>
    </div>
  );
}

function Accordion(props) {
  const [bindIndex, setBindIndex] = React.useState(props.defaultIndex);

  const changeItem = itemIndex => {
    if (typeof props.onItemClick === 'function') props.onItemClick(itemIndex);
    if (itemIndex !== bindIndex) setBindIndex(itemIndex);
  };
  const items = props.children.filter(item => item.type.name === 'AccordionItem');

  return (
    <div className="wrapper">
      {items.map(({ props }) => (
        <AccordionItem
          isCollapsed={bindIndex !== props.index}
          label={props.label}
          handleClick={() => changeItem(props.index)}
          children={props.children}
        />
      ))}
    </div>
  );
}
```



### alert（弹出提示）

对一些消息提醒进行封装

```react
@keyframes leave {
  0% { opacity: 1 }
  100% { opacity: 0 }
}

.alert {
  padding: 0.75rem 0.5rem;
  margin-bottom: 0.5rem;
  text-align: left;
  padding-right: 40px;
  border-radius: 4px;
  font-size: 16px;
  position: relative;
}

.alert.warning{
  color: #856404;
  background-color: #fff3cd;
  border-color: #ffeeba;
}

.alert.error{
  color: #721c24;
  background-color: #f8d7da;
  border-color: #f5c6cb;
}

.alert.leaving{
  animation: leave 0.5s forwards;
}

.alert .close {
  position: absolute;
  top: 0;
  right: 0;
  padding: 0 0.75rem;
  color: #333;
  border: 0;
  height: 100%;
  cursor: pointer;
  background: none;
  font-weight: 600;
  font-size: 16px;
}

.alert .close:after{
  content: 'x';
}
function Notification(props) {
    const [isShown, setIsShown] = React.useState(false);
    const [isLeaving, setIsLeaving] = React.useState(false);

    let timeoutId = null;

    React.useEffect(() => {
      setIsShown(true);
      return () => {
        clearTimeout(timeoutId);
      }
    }, [props.isShown, props.timeout, timeoutId]);

    const closeNotification = () => {
      setIsLeaving(true);
      timeoutId = setTimeout(() => {
        setIsLeaving(false);
        setIsShown(false);
      }, 250)
    }

    return isShown && (
      <div className={`alert ${props.type}${isLeaving ? ' leaving' : ''}`} role="alert">
        <button className="close" onClick={closeNotification} />
        {props.message}
      </div>
    )
}
```



### Carousel（轮播图）un

```react
function Carousel(props) {
  const [active, setActive] = React.useState(0);
  let scrollInterval = null;
  const style = {
    carousel: {
      position: 'relative'
    },
    carouselItem: {
      position: 'absolute',
      visibility: 'hidden'
    },
    visible: {
      visibility: 'visible'
    }
  };
  React.useEffect(() => {
    scrollInterval = setTimeout(() => {
      const { carouselItems } = props;
      setActive((active + 1) % carouselItems.length);
    }, 2000);
    return () => clearTimeout(scrollInterval);
  });
  const { carouselItems, ...rest } = props;
  return (
    <div style={style.carousel}>
      {carouselItems.map((item, index) => {
        const activeStyle = active === index ? style.visible : {};
        return React.cloneElement(item, {
          ...rest,
          style: {
            ...style.carouselItem,
            ...activeStyle
          }
        });
      })}
    </div>
  );
}
```



### Collapse（可折叠盒子）

```react
function Collapse(props) {
  const [isCollapsed, setIsCollapsed] = React.useState(props.collapsed);

  const style = {
    collapsed: {
      display: 'none'
    },
    expanded: {
      display: 'block'
    },
    buttonStyle: {
      display: 'block',
      width: '100%'
    }
  };

  return (
    <div>
      <button style={style.buttonStyle} onClick={() => setIsCollapsed(!isCollapsed)}>
        {isCollapsed ? 'Show' : 'Hide'} content
      </button>
      <div
        className="collapse-content"
        style={isCollapsed ? style.collapsed : style.expanded}
        aria-expanded={isCollapsed}
      >
        {props.children}
      </div>
    </div>
  );
}
```



### CountDown（倒计时）

```react
function CountDown({ hours = 0, minutes = 0, seconds = 0 }) {
  const [paused, setPaused] = React.useState(false);
  const [over, setOver] = React.useState(false);
  const [time, setTime] = React.useState({
    hours: parseInt(hours),
    minutes: parseInt(minutes),
    seconds: parseInt(seconds)
  });

  const tick = () => {
    if (paused || over) return;
    if (time.hours == 0 && time.minutes == 0 && time.seconds == 0) setOver(true);
    else if (time.minutes == 0 && time.seconds == 0)
      setTime({
        hours: time.hours - 1,
        minutes: 59,
        seconds: 59
      });
    else if (time.seconds == 0)
      setTime({
        hours: time.hours,
        minutes: time.minutes - 1,
        seconds: 59
      });
    else
      setTime({
        hours: time.hours,
        minutes: time.minutes,
        seconds: time.seconds - 1
      });
  };

  const reset = () => {
    setTime({
      hours: parseInt(hours),
      minutes: parseInt(minutes),
      seconds: parseInt(seconds)
    });
    setPaused(false);
    setOver(false);
  };

  React.useEffect(() => {
    let timerID = setInterval(() => tick(), 1000);
    return () => clearInterval(timerID);
  });

  return (
    <div>
      <p>{`${time.hours.toString().padStart(2, '0')}:${time.minutes
        .toString()
        .padStart(2, '0')}:${time.seconds.toString().padStart(2, '0')}`}</p>
      <div>{over ? "Time's up!" : ''}</div>
      <button onClick={() => setPaused(!paused)}>{paused ? 'Resume' : 'Pause'}</button>
      <button onClick={() => reset()}>Restart</button>
    </div>
  );
}
```



### FileDrop（文件拖拽）un

```react
.filedrop {
  min-height: 120px;
  border: 3px solid #d3d3d3;
  text-align: center;
  font-size: 24px;
  padding: 32px;
  border-radius: 4px;
}

.filedrop.drag {
  border: 3px dashed #1e90ff;
}

.filedrop.ready {
  border: 3px solid #32cd32;
}
function FileDrop(props) {
  const [drag, setDrag] = React.useState(false);
  const [filename, setFilename] = React.useState('');
  let dropRef = React.createRef();
  let dragCounter = 0;

  const handleDrag = e => {
    e.preventDefault();
    e.stopPropagation();
  };

  const handleDragIn = e => {
    e.preventDefault();
    e.stopPropagation();
    dragCounter++;
    if (e.dataTransfer.items && e.dataTransfer.items.length > 0) setDrag(true);
  };

  const handleDragOut = e => {
    e.preventDefault();
    e.stopPropagation();
    dragCounter--;
    if (dragCounter === 0) setDrag(false);
  };

  const handleDrop = e => {
    e.preventDefault();
    e.stopPropagation();
    setDrag(false);
    if (e.dataTransfer.files && e.dataTransfer.files.length > 0) {
      props.handleDrop(e.dataTransfer.files[0]);
      setFilename(e.dataTransfer.files[0].name);
      e.dataTransfer.clearData();
      dragCounter = 0;
    }
  };

  React.useEffect(() => {
    let div = dropRef.current;
    div.addEventListener('dragenter', handleDragIn);
    div.addEventListener('dragleave', handleDragOut);
    div.addEventListener('dragover', handleDrag);
    div.addEventListener('drop', handleDrop);
    return function cleanup() {
      div.removeEventListener('dragenter', handleDragIn);
      div.removeEventListener('dragleave', handleDragOut);
      div.removeEventListener('dragover', handleDrag);
      div.removeEventListener('drop', handleDrop);
    };
  });

  return (
    <div
      ref={dropRef}
      className={drag ? 'filedrop drag' : filename ? 'filedrop ready' : 'filedrop'}
    >
      {filename && !drag ? <div>{filename}</div> : <div>Drop files here!</div>}
    </div>
  );
}
```





### Modal（模态框）

```react
.modal {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  width: 100%;
  z-index: 9999;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgba(0, 0, 0, 0.25);
  animation-name: appear;
  animation-duration: 300ms;
}

.modal-dialog {
  width: 100%;
  max-width: 550px;
  background: white;
  position: relative;
  margin: 0 20px;
  max-height: calc(100vh - 40px);
  text-align: left;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
  -webkit-animation-name: animatetop;
  -webkit-animation-duration: 0.4s;
  animation-name: slide-in;
  animation-duration: 0.5s;
}

.modal-header,
.modal-footer {
  display: flex;
  align-items: center;
  padding: 1rem;
}
.modal-header {
  border-bottom: 1px solid #dbdbdb;
  justify-content: space-between;
}
.modal-footer {
  border-top: 1px solid #dbdbdb;
  justify-content: flex-end;
}
.modal-close {
  cursor: pointer;
  padding: 1rem;
  margin: -1rem -1rem -1rem auto;
}
.modal-body {
  overflow: auto;
}
.modal-content {
  padding: 1rem;
}

@keyframes appear {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
@keyframes slide-in {
  from {
    transform: translateY(-150px);
  }
  to {
    transform: translateY(0);
  }
}
function Modal({ isVisible = false, title, content, footer, onClose }) {
  React.useEffect(() => {
    document.addEventListener('keydown', keydownHandler);
    return () => document.removeEventListener('keydown', keydownHandler);
  });

  function keydownHandler({ key }) {
    switch (key) {
      case 'Escape':
        onClose();
        break;
      default:
    }
  }

  return !isVisible ? null : (
    <div className="modal" onClick={onClose}>
      <div className="modal-dialog" onClick={e => e.stopPropagation()}>
        <div className="modal-header">
          <h3 className="modal-title">{title}</h3>
          <span className="modal-close" onClick={onClose}>
            &times;
          </span>
        </div>
        <div className="modal-body">
          <div className="modal-content">{content}</div>
        </div>
        {footer && <div className="modal-footer">{footer}</div>}
      </div>
    </div>
  );
}
```





### Tabs（标签菜单）

```react
.tab-menu > button {
  cursor: pointer;
  padding: 8px 16px;
  border: 0;
  border-bottom: 2px solid transparent;
  background: none;
}
.tab-menu > button.focus {
  border-bottom: 2px solid #007bef;
}
.tab-menu > button:hover {
  border-bottom: 2px solid #007bef;
}
function TabItem(props) {
  return <div {...props} />;
}

function Tabs(props) {
  const [bindIndex, setBindIndex] = React.useState(props.defaultIndex);
  const changeTab = newIndex => {
    if (typeof props.onTabClick === 'function') props.onTabClick(newIndex);
    setBindIndex(newIndex);
  };
  const items = props.children.filter(item => item.type.name === 'TabItem');

  return (
    <div className="wrapper">
      <div className="tab-menu">
        {items.map(({ props: { index, label } }) => (
          <button onClick={() => changeTab(index)} className={bindIndex === index ? 'focus' : ''}>
            {label}
          </button>
        ))}
      </div>
      <div className="tab-view">
        {items.map(({ props }) => (
          <div
            {...props}
            className="tab-view_item"
            key={props.index}
            style={{ display: bindIndex === props.index ? 'block' : 'none' }}
          />
        ))}
      </div>
    </div>
  );
}
```





### Tooltip(消息提示框)

```react
.tooltip {
  position: relative;
  background: rgba(0, 0, 0, 0.7);
  color: white;
  visibility: hidden;
  padding: 5px;
  border-radius: 5px;
}
.tooltip-arrow {
  position: absolute;
  top: 100%;
  left: 50%;
  border-width: 5px;
  border-style: solid;
  border-color: rgba(0, 0, 0, 0.7) transparent transparent;
}
function Tooltip({ children, text, ...rest }) {
  const [show, setShow] = React.useState(false);

  return (
    <div>
      <div className="tooltip" style={show ? { visibility: 'visible' } : {}}>
        {text}
        <span className="tooltip-arrow" />
      </div>
      <div {...rest} onMouseEnter={() => setShow(true)} onMouseLeave={() => setShow(false)}>
        {children}
      </div>
    </div>
  );
}
```





### TreeView（树状图）

```react
.tree-element {
  margin: 0;
  position: relative;
}

div.tree-element:before {
  content: '';
  position: absolute;
  top: 24px;
  left: 1px;
  height: calc(100% - 48px);
  border-left: 1px solid gray;
}

.toggler {
  position: absolute;
  top: 10px;
  left: 0px;
  width: 0;
  height: 0;
  border-top: 4px solid transparent;
  border-bottom: 4px solid transparent;
  border-left: 5px solid gray;
  cursor: pointer;
}

.toggler.closed {
  transform: rotate(90deg);
}

.collapsed {
  display: none;
}
function TreeView({
  data,
  toggled = true,
  name = null,
  isLast = true,
  isChildElement = false,
  isParentToggled = true
}) {
  const [isToggled, setIsToggled] = React.useState(toggled);

  return (
    <div
      style={{ marginLeft: isChildElement ? 16 : 4 + 'px' }}
      className={isParentToggled ? 'tree-element' : 'tree-element collapsed'}
    >
      <span
        className={isToggled ? 'toggler' : 'toggler closed'}
        onClick={() => setIsToggled(!isToggled)}
      />
      {name ? <strong>&nbsp;&nbsp;{name}: </strong> : <span>&nbsp;&nbsp;</span>}
      {Array.isArray(data) ? '[' : '{'}
      {!isToggled && '...'}
      {Object.keys(data).map((v, i, a) =>
        typeof data[v] == 'object' ? (
          <TreeView
            data={data[v]}
            isLast={i === a.length - 1}
            name={Array.isArray(data) ? null : v}
            isChildElement
            isParentToggled={isParentToggled && isToggled}
          />
        ) : (
          <p
            style={{ marginLeft: 16 + 'px' }}
            className={isToggled ? 'tree-element' : 'tree-element collapsed'}
          >
            {Array.isArray(data) ? '' : <strong>{v}: </strong>}
            {data[v]}
            {i === a.length - 1 ? '' : ','}
          </p>
        )
      )}
      {Array.isArray(data) ? ']' : '}'}
      {!isLast ? ',' : ''}
    </div>
  );
}
```

