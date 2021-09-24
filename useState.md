# confusing, definitely, obvious, miniseries, cover, depth, below, 
# minus, middle, account, counter, modify, implement, execute, version
# equal, notice, iteration, decreasing, re-render, incorrect, subtract
# row, complexity, functionality, constructor, performance, inspect, drag 
# initial, merge, automatically, 

# 1. you can only use hooks inside of function components, can not use in class component
#    (because classes already have their own way to do that the same things that hooks do)
# 2. every time your component runs so this function runs your hooks must execute in the exact
#    same order that means for example if you have a useState hook here, they always muse execute
#    in the same order 
```js
useState()
useState()
useState()
useState()
```
# if you put a hook inside of if check 
```js
if(this == something){
    useState()
}
useState()
useState()
useState()
```
# now this first hook may run or it may not run, so you're going to get errors
# we're saying that you cannot put a hook inside of a if check right here
# if we have if(true), u will get a error that says react hook useState us called
# conditionally, react hooks must be called in the same exact order in every component render
## so you cannot put hooks inside of if statements you cannot put them inside of functions 
## you cannot put them inside of loops, they cannot be nestedd in anything
## they must be at the top level essentially nothing around them no blocks
## at the top level of your function always called in the exact same order

# 3. useSate its a function and the thing that we passed to useState is going to be 
#    what the default state is
```js
useState(4) // our counter is going to start at 4
// useState is going to return to us an array of values 
// nobody's going to actually write this out as an array what they're going to do 
// is D structure, because you state always return an array with two values
// the first is current state, 
// the second thing is the function that allows you to update that current state
const [count, setCount] = useState(4)
// how to use setCount, set up a onClick on the button <button onClick = {decrementCount}>
```
```js
function App(){
    const [count, setCount] = useState(4)

    function decrementCount(){
        // the incorrect to update a value to base on the previous value 
        //setCount(count - 1) pass in a function 
        //setCount(()=>{
        //    count -1
        //})
        setCount(prevCount => prevCount - 1)
    //the reason that we're doing this if we just go back here a little ways to our old version
        // setCount(count - 1)
        // setCount(count - 1)
        // if we were to call setCount two times in a row, now what should happen is we 
        // decrease by two every single time we click, but you're gonna notice it still
        // only decreases by one and the reason for this our count value here is just the 
        // value of count when we render our function 
        // so we're calling setCount of 4 minus at the first setCount() which is gives us 3
        // we're calling the exact same in second setCount() of 4 minus 1 which 3 again
        // NBNBNB so anytime you're modifying state where you actually are using the previous
        // value of your state to create the new value you need to make sure your use the 
        // function version of setting your state just like if you were setting state inside of 
        // a class component 
    }

    function incrementCount(){
        setCount(prevCount => prevCount + 1)
    }

    return(
        <>
            <button onClick = {decrementCount}>-</button>
            <span>{count}</span>
            <button onClick = {incrementCount}>+</button>
        </>
    )
}
```
# 8:58
# in the function component, the value of 4 gets called every single time
# we run our function
# useState has two ways to pass in the state 
# the first way is you just pass the state like this useState(4)
# the second way take a function inside of useState
```js
const [count, setCount] = useState(()=>{
        console('run function')
        return 4
    })
// run this funciton the only first time your component renders
```
# we can instead let's create a simple function 
```js
function countInitial(){
        console('run function')
        return 4
} // run this function every single time
function App(){
    const [count, setCount] = useState(countInitial())
}
```
# (1)
# so the important thing to notice is that if you are doing something 
# where you have to have really complex slow computation for your initial state
# make sure you use the function version instead so it only ever gets ran one time
# useState(()=>(countInitial()))   这种情况函数只会运行一次，上面的情况会每次都运行
# (2)
# another important notice that you state works a little bit differently
# than the state and class components when dealing with object
# so let's say that we had an object here for example we had of count 
```js
function App(){
    const [state, setState] = useState({count:4, theme:'blue'})
    const count = state.count
    const theme = state.theme
    
    function decrementCount(){
        setState(prevState => {
            return {count: prevState.count - 1}
        })
    }

    return(
        <>
            <button onClick = {decrementCount}>-</button>
            <span>{count}</span>
            <span>{theme}</span>
            <button onClick = {incrementCount}>+</button>
        </>
    )
}
// 会显示4和blue，当点击按钮的时候，4会变成3， 但是blue会消失
// value change down to 3 which is correct but our theme completely disappeared
// that because with useState hook what is happening is whenever we set our state
// to value it's not merging an object like it would do with setState inside of class component
// we're just merges this object with our correct state, 
// its actually overriding all of our old state
// so everything inside of our state is being overridden with whatever we pass out down here
```
# so if you want to use an object, you need to do spread out your previous state 
# and then set your new state
# return {...prevState, count: prevState.count - 1}
# 当使用对象的时候，如果想更新和改变内容，要使用扩展运算，这样原来的内容不会丢失或覆盖
# the reason that the automatic merging does not happen 
# because in general when you're using state inside of a hook like this


# what you're going to want to do is actually have multiple state hooks 
# so instead of having one state hook here, we would have two 
# the first one is going to be for our a ccount and the second setCount
# now we want to have our theme be added in here 
```js
function App(){
    const [count, setCount] = useState(4)
    const [theme, setTheme] = useState('blue')

    function decrementCount(){
        setCount(prevCount => prevCount - 1)
    }

    function incrementCount(){
        setCount(prevCount => prevCount + 1)
        setTheme('red')
    }

    return(
        <>
            <button onClick = {decrementCount}>-</button>
            <span>{count}</span>
            <span>{theme}</span>
            <button onClick = {incrementCount}>+</button>
        </>
    )
}
// we can modify our count, our theme doesn't get modified
// because we use two different hooks to manage our different types of state
// benefit of useState, u can have multiple different pieces of state
```