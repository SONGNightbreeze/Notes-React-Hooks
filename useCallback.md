# toggle, kind of, entire, larger, a sequence of, allow, straightforward, run into
# recommend,  concepts, wrap, brand 
```js
// App.js
// toggle the theme on the background color, we can change the number input
// and it's going to change the list of numbers that are displayed down here
import React, { useState } from 'react'
import List from './List.js'

export default function App(){
    const [number, setNumber] = useState(1)
    const [dark, setDark] = useState(false)
    // 给子组件传入状态的函数， a function that calls out to some API somewhere
    // and returns to us some results 
    const getItems =()=>{
        return [number, number + 1, number + 2]
    }
    // 修改背景，直接传给style即可
    const theme ={
        backgroundColor: dark ? '#333' : '#FFF',
        color: dark ? '#FFF' : '#333'
    }

    return (
        <div style={theme}>
            <input
                type="number"
                value={number}
                onChange={e => setNumber(parseInt(e.target.value))}
            />
            // 设置按钮来修改dark的状态，来修改背景
            <button onClick={() => setDark(prevDark => !prevDark)}>
                Toggle theme
            </button>
            <List getItems={getItems} />
        </div>
    )
}

```

```js
// List.js
import React, { useEffect, useState } from 'react'

export default function List({ getItems }){
    const [items, setItems] = useState([])

    useEffect(()=>{
        setItems(getItems())
        console.log('Updating Items')
    }, [getItems]) // get items function inside of app component 问题出现的原因
                    // is being recreated every single time App里面的getItem函数将被运行一次又一次
                    // that where we want to use the callback hook
// [getItems] changes every time because we create a brand new function 
// but useCallback we're not creating a new function unless we need to 
// so the go referential equality of get items from the first time it rendered
// and get items the next item it renders is going to be the same as long as 
// number input here doesn't actually change
    return items.map(item => <div key={item}>{item}</dov>)
}
// when we click on toggle theme you're gonna see it also calls
// that updating items and logs that out
// this is problem and this can be fixed with the use callback hook
```
# so we use callback hook so what used callback does is just like use memo 
# it's not goint to rerun the code inside of it unless certain parameters change
# and that means everytime single time we call out app component here,
# this get items function is only going to update when it actually needs to  
```js
import React, { useState, useCallback } from 'react'
import List from './List.js'

export default function App(){
    const [number, setNumber] = useState(1)
    const [dark, setDark] = useState(false)

    const getItems = useCallback(()=>{
        return [number, number + 1, number + 2]
    },[number]) // the second parameter to this use callback is going to be our 
                // array of dependencies

    const theme ={
        backgroundColor: dark ? '#333' : '#FFF',
        color: dark ? '#FFF' : '#333'
    }

    return (
        <div style={theme}>
            <input
                type="number"
                value={number}
                onChange={e => setNumber(parseInt(e.target.value))}
            />
            <button onClick={() => setDark(prevDark => !prevDark)}>
                Toggle theme
            </button>
            <List getItems={getItems} />
        </div>
    )
}
//  we toggle our theme, it doesn't actually update our items
//when the reason for this, callback only recreates our get items function when the number
// changes and it's not going to recreate that when this dark variable changes 
```
# 用法和 useMemo 相似，
# the one big difference between useMemo and useCallback is that 
# useMemo it takes a function and it's going to return to you 
# the return value of that function
# useCallback is different it takes a function that 
# is actually what the useCallback returns 

# this example if we had useMemo instead of useCallback
# what would happen is get items would be just set to this array here
# instead of being set to this entire function
# while useCallback since we're returning the actual function we pass to it 
# get items is being set to this entire function and not just return value of that function
# which allow us to use this as a function later on in our application here 
#                                                       (List.js里的)setItems(getItems())
# where we actually call get items and this allows us to pass parameters 
# to this function let's say we wanted pass a parameter of 5 here setItems(getItems(5))
# we gonna increment every single number inside of here by 5
# since this used callback just return this function here, we can just have the first
# parameter of this function inside of useCallback be that number
# so we can just say       
```js
const getItems = useCallback((incrementor)=>{
    return [number + incrementor, number + 1 + incrementor, number + 2 + incrementor]
}, [numer])
// 每个值都自动增加5，来自List.js 里的 setItems(getItems(5))
```
# useCallback 会返回整个函数，useMemo只会返回函数的返回值

# the only reason you would ever want to use this callback hook is 
# if you need to worry about referential equality which is similar 
# to why you would useMemo