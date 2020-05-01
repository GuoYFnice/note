# react

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

