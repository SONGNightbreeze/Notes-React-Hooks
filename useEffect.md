```js
import React, {useState, useEffect} from 'react'

export default function App(){
    const [resourceType, setResourceType] = useState('posts')

    useEffect(()=>{
        console.log('render')
    }, [resourceType])
    // seconde parameter is array that is going to be value 
    // that want to change the hook is going to run
    // for example [resourceType] every single time resourceType changes,
    // the hook will run 
    // only happen when resourceType change, obviously page is changing
    // so when hit the button Posts a bunch of times, render never happening again  
    return (
        <>
            <div>
                <button onClick={()=> setResourceType('posts')}>Posts</button>
                <button onClick={()=> setResourceType('users')}>Users</button>
                <button onClick={()=> setResourceType('comments')}>Comments</button>
            </div>
            <h1>{resourceType}</h1>
        </>
    )
}
```
```js
import React, {useState, useEffect} from 'react'

export default function App(){
    const [resourceType, setResourceType] = useState('posts')
    
    console.log('render')

    useEffect(()=>{
        console.log('resource type changed')
    }, [resourceType])
    return (
        <>
            <div>
                <button onClick={()=> setResourceType('posts')}>Posts</button>
                <button onClick={()=> setResourceType('users')}>Users</button>
                <button onClick={()=> setResourceType('comments')}>Comments</button>
            </div>
            <h1>{resourceType}</h1>
        </>
    )
}
// every time hit the button, on the page console show 
// render
// render
// resource type changed

// if we hit the same button again, we only get 
// render
// render
```
> the important to understand, useEffect only get the run the code inside of it
> whenever the option inside of array actually change
> so u can do something unique, create that hook, only ever run onMount 
> just passing empty in the array
```js
useEffect(()=>{
    console.log('onMount')
},[])  
// only run one time, how many times we change thing 
// we will never run the hook again , because this empyt array
// never changes between different renders 
```
> so the way we think about, is essentially all the value that i want to change
> then you want to do something
```js
import React, {useState, useEffect} from 'react'

export default function App(){
    const [resourceType, setResourceType] = useState('posts')

    useEffect(()=>{
        fetch(`https://jsonplaceholder.typicode.com/${resourceType}`)
            .then(response => response.json())
            .then(json => console.log(json))
    }, [resourceType])
    return (
        <>
            <div>
                <button onClick={()=> setResourceType('posts')}>Posts</button>
                <button onClick={()=> setResourceType('users')}>Users</button>
                <button onClick={()=> setResourceType('comments')}>Comments</button>
            </div>
            <h1>{resourceType}</h1>
        </>
    )
}
// we can see the console, Array(100) is 100 different posts print out 
// because the first time code run resourceType are obviously different, fetch request runs
// now we change the users you can see 10 different users thing being printed 
// the same thing happen change the comments
```
#### take stpes further, set this value to somethine from state
```js
import React, {useState, useEffect} from 'react'

export default function App(){
    const [resourceType, setResourceType] = useState('posts')
    const [items, setItems] = useState([])

    useEffect(()=>{
        fetch(`https://jsonplaceholder.typicode.com/${resourceType}`)
            .then(response => response.json())
            .then(json => setItems(json))
    }, [resourceType])

    return (
        <>
            <div>
                <button onClick={()=> setResourceType('posts')}>Posts</button>
                <button onClick={()=> setResourceType('users')}>Users</button>
                <button onClick={()=> setResourceType('comments')}>Comments</button>
            </div>
            <h1>{resourceType}</h1>
            {Items.map(item => {
                return <pre>{JSON.stringify(item)}</pre> // convert to string
                //you can see all the posts being printed out here
                // we change users and comments we have all the different user and comment
            })}
        </>
    )
}

```
#### more complicate than this
```js
import React, {useState, useEffect} from 'react'

export default function App(){
    const [windowWidth, setWindowWidth] = useState(window.innerWidth) 
    // we need to do set up a code listen to the window resize 
    // and modify the innerWidth variable

    const handleResize =()=>{
        setWindowWidth(window.innerWidth)
    }

    useEffect(()=>{
        // whenever resize, we want to call the function handleResize
        window.addEventListener('resize', handleResize)

        // end up this listener, delete App component and unMount, remove eventListener
        // otherwise its gonna to slow down App
        return ()=>{
            window.removeEventListener('resize', handleResize)
        }
    },[]) // [] empty array is DidMount 

    return (
        <div>{windowWidth}</div>

    )
}
```

```js
import React, {useState, useEffect} from 'react'

export default function App(){
    const [resourceType, setResourceType] = useState('posts')

    useEffect(()=>{
        console.log('resource changed')

        return ()=>{
            console.log('return from resource change')
        }
    }, [resourceType])

    return (
        <>
            <div>
                <button onClick={()=> setResourceType('posts')}>Posts</button>
                <button onClick={()=> setResourceType('users')}>Users</button>
                <button onClick={()=> setResourceType('comments')}>Comments</button>
            </div>
            <h1>{resourceType}</h1>
        </>
    )
}
// we will get in the console
// return from resource change
// resource changed
// run the return code, then setup code
// the reason happened, return code is essentially consider cleanup
// so every single time useEffect get run, whatever return get run first to cleanup 
// what u did the last time 
// so we can set up ListenerEvent here, we can make sure to cleanup the code 
// and remove that in return 
// this return is going to be called anytime you component unMount,

```
