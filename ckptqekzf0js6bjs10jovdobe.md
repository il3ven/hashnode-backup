## Common Error: Accidentally Mutating State in React

In React, the state is immutable. In simple terms it means that you should not modify it directly. Instead a new object should be created to set the state using `setState`.

Here are two examples.

#### Modifying the state directly - Not Acceptable
```js
onChange(event) {
  this.state.value = event.target.value
}
```

#### Using `setState()` - Acceptable
```js
onChange(event) {
  this.setState({ value: event.target.value })
}
```

The above is clear to almost all react developers. However, developers still make the above mistake accidentally. Have a look at the code snippet below.

#### Common Mistake
```js
const [arr, setArr] = useState([])

const handleSubmit = (event) => {
  event.preventDefault()
    
  arr.push("New Item")
  setArr(arr)
}
```

In the above code snippet the developer did use `setArr` but still modified the `arr`. The `.push()` modifies the `arr`.

#### Why will the above code not work?

React compares the previous state with the updated state to decide if the component needs to be re-rendered. Modifying the state directly will disturb this process. As a result the component will behave unexpectedly. In some cases not re-rendering at all even though the state has been modified.

The above mistake is independent of functional or class components. 

#### Solution
```js
const [arr, setArr] = useState([])

const handleSubmit = (event) => {
  event.preventDefault()

  setArr([...arr, "new value"])
}
```

The spread syntax creates a copy of the array. Hence we are not modifying the original array.

#### Subtler Way of Making the Same Mistake
```js
const [obj, setObj] = useState({
  key: 'value',
})

const handleSubmit = (event) => {
  event.preventDefault()

  const tempObj = obj
  tempObj.key = "new value"
  setObj(tempObj)
}
```

In the above snippet, it may seem at first that we made a copy of `obj` and modified that but in JavaScript the objects copied through reference. In other words, `tempObj` and `obj` are the same. Any changes made to `tempObj` is also reflected on `obj`.

### CodeSandbox Demo

%[https://codesandbox.io/embed/mutating-state-functional-component-w3cpt?theme=dark]
