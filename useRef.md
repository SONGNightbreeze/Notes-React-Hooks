# flexible, prevent, access, interact, warn

```js
import React, {useState, useEffect, useRef} from 'react'

export default function App(){
    const [name, setName] = useState('')
    const [renderCount, setRenderCount] = useState(0)

    useEffect(()=>{
        setRenderCount(prevRenderCount => prevRenderCount + 1)
    })

    return (
        <>
            <input value={name} onChange={e => setName(e.target.value)}/>
            <div> My name is {name} </div>
            // if you want to show the number of times a component rendered on the screen
            <div>I rendered {renderCount} times </div>
        </>
    )
}
// update our render count every time we render our component 
// but a huge red flag with this is that when you update your state 
// u cause the component to re-render, so the first time i component renders
// is gonna set this state which causes it to re-render and then just gonna set 
// again .....infinite loop  
```
# ref is similar with state and that it persist between renders of your component 
# but the important thing to note about refs versus state is that a ref does not
# cause your component to re-update when it gets changed 
# so instead of using state, we useRef 

```js
import React, {useState, useEffect, useRef} from 'react'

export default function App(){
    const [name, setName] = useState('')
    const renderCount = useRef(0)
    // this is actually returns an object, this object looks like has a single property
    // called current,  { current: 0 }

    useEffect(()=>{
        //setRenderCount(prevRenderCount => prevRenderCount + 1)
        renderCount.current = renderCount.current + 1
        // 下面展示的部分也要变成 renderCount.current
    })

    return (
        <>
            <input value={name} onChange={e => setName(e.target.value)}/>
            <div> My name is {name} </div>
            // if you want to show the number of times a component rendered on the screen
            <div>I rendered {renderCount.current} times </div>
        </>
    )
}
// now we change our name in the input 在input框里面输入内容
// so actually update our renderCount so change that you can see {renderCount.current}
// render 2times ..... 每输入一个字母，renderCount.current就会+1
// the important thing to note is that we can change this rendeCount.current
// as much as we want as many times as we want and we'll never cause our component to re-render
// because it's completely separate from our component render cycle
```
# the important thing about ref it's very similar to state and that u can store a previous value
# in it and it persists between different render but it does not cause u to re-render like state

# that's basic use cases for using refs but probably the biggest use case that people 
# are going to use ref is to reference elements inside of your HTML 
# flexible, prevent, 

```js
import React, {useState, useEffect, useRef} from 'react'

export default function App(){
    const [name, setName] = useState('')
    const renderCount = useRef()

    function focus() ={
        // console.log(inputRef)
        // 输入 <input value> is exactly the same as if u were to use document
        // query selector for example to get this input it gives you the exact same dom node
        inputRef.current.focus()
        // 可以影响input里面的值
        inputRef.current.value ='some value'
    } 

    return (
        <>
            // that each element inside of your document has a ref attribute
            <input ref={inputRef} value={name} onChange={e => setName(e.target.value)}/>
            <div> My name is {name} </div> 
            // 因为展示 <input>里面的name，展示的就是输入框输入的内容
            <button onClick={focus}> Focus </button>
        </>
    )
}
// so now whenever our input ref gets rendered on the screen
// it's going to set this input ref variable equal to this document <input>
```
# sometimes use refs in order to do this on change stuff 
# so instead of using state and using the on change to update ur state inside of this ref

# another great use cases for refs is to store something like the previous value
# of your state because there's no way to get the previous value of state
# it's just always the value of state 
# so what happens if you want to store the previous value of your state
# flexible, prevent, 

```js
import React, {useState, useEffect, useRef} from 'react'

export default function App(){
    const [name, setName] = useState('')
    const prevName = useRef('')

    useEffect(()=>{
        // 将name赋值给了prevName.current
        prevName.current = name
    },[name])  // 因为这里只有当name变化的时候才会触发，所以第一次不会触发，自然就会保留之前的值

    return (
        <>
            <input value={name} onChange={e => setName(e.target.value)}/>
            <div> My name is {name} and it used to be {prevName.current}</div>
        </>
    )
}
// {name}就显示在输入框中输入的完整的内容， {prevName.current}就显示上一步输入的内容
// if u using state for example when u wanted to set previous name instead of a state variable
// it would cause your component to re-render again but by using refs here we're not causing
// additional re-render which we dont actually need 
```
# so ref are really useful for not only accessing dom elements but they're really useful 
# for persisting values across renders without actually causing a re-render
# otherwise insido of a function component there is no way to persist values between renders
# in class component you can use class variable and such to persist values between renders both 
# function components you need to use refs in order to persist a value between renders
# if you're not using state