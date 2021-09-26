# diving into, towards, platform, redundancy, boolean, delay, performance
# built a way to solve, memorization, catch, recompute, instantaneous, go through

```js
import React, {useState} from 'react'

export default function App(){
    const [number, setNumber] = useState(0)
    const [dark, setDark] = useState(flase)
    const doubleNumber = slowFunction(number)
    const themeStyles = {
        backgroundColor: dark ? 'black' : 'white',
        color: dark ? 'white' : 'black'
    }

    return (
        <>
            <input type="number" value={number} onChange={e => setNumber(parseInt(e.target.value))} />
            <button onClick={() => setDark(prevDark => !prevDark)}>Change Theme</button>
            <div style={themeStyle}>{doubleNumber}</div>
        </>
    )
}

function slowFunction(num){
    console.log('Calling Slow Function')
    // loop doint nothing for a really long time 
    // to emulate what would happen in a slow long-running complex function 
    for (let i = 0; i <= 100000000; i++){}
    return num * 2
}
// problme
// slowness is not only gonna be slow update our number 
// if we click our change theme button you're gonna notice we have the same delay
// 点击按钮转换背景一样会有延迟
// the reason for that is because one you update state and react
// it's going to re-render your entire component
// so it's going to run this entire App function from top to bottom
// which means this slow function get called every single time that we render
// App component so whether we're changing the theme whether we're updating 
// the number here or whether some other component is causing this component 
// to re-render you're going to be forcing it to go through that slow function 
// over and over again     
```
# 使用 useMemo 来解决这个问题
```js
import React, {useState, useMemo} from 'react'

export default function App(){
    const [number, setNumber] = useState(0)
    const [dark, setDark] = useState(flase)
    // slowFunction here, takes input of a number and it's always going to give us 
    // the same output every time we give it the same input
    // so we can catch taht input value number and the output it give us that way 
    // if the number doesn't change we dont have to recalculate our solo function 
    // useMemo pass it in a function 
    const doubleNumber = useMemo(() => {
        return slowFunction(number)
    }, [number])

    const themeStyles = {
        backgroundColor: dark ? 'black' : 'white',
        color: dark ? 'white' : 'black'
    }

    return (
        <>
            <input type="number" value={number} onChange={e => setNumber(parseInt(e.target.value))} />
            <button onClick={() => setDark(prevDark => !prevDark)}>Change Theme</button>
            <div style={themeStyle}>{doubleNumber}</div>
        </>
    )
}

function slowFunction(num){
    console.log('Calling Slow Function')
    for (let i = 0; i <= 100000000; i++){}
    return num * 2
}
// when we go through our code we click Change theme it causes our component to re-render
// so it calls this function does all these stuffs and it gets down to useMemo
// and it says well our number is exactly the same as what it was last time
// so we're not gonna recall this slow function, because we already know what 
// the result is, it's the exact same thing as it was last time 
// so we're saving ourselves from having to recalculate this number with the slow function
// and we only forcing ourselves to do this when we update this number instead of our input
// so we only running this slow code when we have to and not running it when we dont need 
```
# the reason you dont want to memorize everything 
# because it does give you some performance overheads and some memory overhead
# for example this useMemo function must be called every single render of your component
# so you're calling an additional function and also it's saving the value this previous
# value in some memory variable so you're forcing your memory to get larger every time
# you useMemo because you have to store an additional variable in memory to store that 
# previous value 
# if you start to do this everywhere in your application especially where u dont need it 
# it's going to cause additional memory usage and additional performance problems
# when just not using it would have been better
# so only useMemo in this case when you actually need the performance benefits
# when the function you're calling is incredibly slow
# second case for useMemo
# that is something called the referential equality and if you aren't really familiar 
# with value versus reference in JavaScript
# when you try to compare two different variables in JavaScript it's going to compare
# the reference in the case of objects and arrays for example theme styles

```js
    const themeStyles = {
        backgroundColor: dark ? 'black' : 'white',
        color: dark ? 'white' : 'black'
    }
    const themeStyles2 = {
        backgroundColor: dark ? 'black' : 'white',
        color: dark ? 'white' : 'black'
    }
// themeStyles and themeStyles2 are equal to each other 
// but JavaScript they reference two different objects
// they just have the same values in the object 
// but the reference to the object itself is different
// these two values are not equal to each other
```

```js
    const themeStyles = {
        backgroundColor: dark ? 'black' : 'white',
        color: dark ? 'white' : 'black'
    }
    useEffect(() =>{
        console.log('Theme Changed')
    }, [themeStyles])
// if we changed the number, it's alse causing this theme change to be run
// and the reason for that referential equality what's happening is every time 
// we run our function, we get a new theme styles object being created 
// and the new theme style object is not the same as the old theme styles
// even though they have the exact same values in the object 
// the reference different places in memory and that's really important to know 
// so make sure that we only ever run useEffect when our theme style object
// gets object we can useMemo again 
    const themeStyles = useMemo(() => {
        return {
            backgroundColor: dark ? 'black' : 'white',
            color: dark ? 'white' : 'black'
        }
    }, [dark])
    useEffect(() => {
        console.log('Theme Changed')
    }, [themeStyles])
// we are wrapping this object inside of memorisation, 
// if our dark variables doesn't change we dont read out theme styles 
// so we get the exact same reference as we had the previous time we rendered  our app
// so now our useEffect is comparing our old theme styles with our new theme styles
// but they both reference the exact same object 
```
# so these are the two big use cases for the useMemo
# 1. when you want to make a slow function we wrap it in this useMemo 
#    so that doesn't recompute every single time you render your component 
#    and it only computes when you actually need the value from the function 
#    since the inputs actually changed 
# 2. idea of a referential equality whenever you want to make sure the reference of a object
#    or array is exactly the same as it was the last time you rendered if none of the internal
#    workings changed you're gonna want to useMemo here to make sure that you only 
#    update the reference of that object whenever the actual contents of the object changed\
#    instead of updating every single time you render 

