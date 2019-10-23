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

### DataList

使用传参控制列表是否为有序

```react
function DataList({ isOrdered , data }){
	const list = data.map((val,i) => <li key={`${i}_${val}`>{val}</li>);
    return isOrdered ? <ol>{list}</ol> : <ul>{list}</ul>
}
```

### DataTable

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

### MappedTable

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

