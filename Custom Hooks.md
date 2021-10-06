## App.js
```js
import React, {useState} from 'react'

export default function App(){
    const [name, setName] = useState('')

    return (
        <input
            type="text"
            value={name}
            onChange={e => setName(e.target.value)}
        />
    )
}
```
# create a custom hook which is going to allow us to store variables 
# inside of local storage but we can use it just like we would useState

# when you're creating a custom hook, the first thing u need to do 
# give it some form of name and you want that name to start with use 
# this is just a convention that react uses for creating custom hook

# useLocalStorage.js
```js
import React from 'react'
// i want this hook to work the same as useState
// exactly return the exact same thing as useState

// we need to do setup some code for taking in our initial value因为useState有默认值的功能
export default function useLocalStorage(initialValue){
    // inside of hook, we can use other hooks built-in to react
    const [value, setValue] = useState(initialValue)

    return [value, setValue]
    // return from this local storage is exactly the same as useState over here
    // 和useState的功能一样 
}
```
# App.js
```js
import React from 'react'
import useLocalStorage from './useLocalStorage'

export default function App(){
    const [name, setName] = useLocalStorage('')
    // .....
```
-------------------------------------------------------------
# let's create the logic for persisting our data as well as for taking our data
# out of local storage 
```js
import {useState} from 'react'

//getting our data 
// the key is going to be what we want to store this as in local storage
// so we need to get this in from our local storage 
// in App  const [name, setName] = useLocalStorage('name','')
function getSavedValue(key, initialValue){
    // we have to use that key to get our saved value
    //localStorage.getItem(key)
    // this is going to get whatever we hae stored at that key location
    // we just need to convert this to JSON 
    const savedValue = JSON.parse(localStorage.getItem(key)) // store the value
    // if we have a saved value so if we previously were on this page 
    // and saved some information we just want to return savedValue
    if (savedValue) return savedValue

    //useState can take not only a value but it can also take a function 
    if (initialValue instanceof Function) return initialValue()
    return initialValue
}

export default function useLocalStorage(initialValue){
    // the reason we're using the function version here 
    // because we dont want to called local storage every time we rent our component
    // it's pretty slow, so we're only going to this once the first time our component
    // when it needs to get that initial value
    const [value, setValue] = useState(() => {
        return getSavedValue(key, initialValue)
    })

    return [value, setValue]
}
// if input something as before and because there's nothing is saved value 
// so it's just going to skip  const savedValue = JSON.parse(localStorage.getItem(key))
// and return down here our initial value 
```
# and we can even check to make sure the function version works 
# in App const [name, setName] = useLocalStorage('name', () => '')
# and passing a function that's going to return an empty string 
# and if we save we should still get everything working as before

# and now inside of this used local storage hook 
# we need to write our code for updating and saving our value to local storage
# we need to useEffect
```js
import {useState, useEffect} from 'react'

function getSavedValue(key, initialValue){

    const savedValue = JSON.parse(localStorage.getItem(key)) 
    if (savedValue) return savedValue
 
    if (initialValue instanceof Function) return initialValue()
    return initialValue
}

export default function useLocalStorage(initialValue){

    const [value, setValue] = useState(() => {
        return getSavedValue(key, initialValue)
    })

    // we want this useEffect to save our value inside of storage
    // we can just set an actual value we first need to stringify this 
    // because you can only ever pass string values to local storage
    // so we're going to convert this value to a string no matter what 
    // before we save it to local storage 
    useEffect(() =>{
        localStorage.setItem(key, JSON.stringify(value))
    }, [value])

    return [value, setValue]
}
```
---------------------------
# another custom hook 
# i want to every time a variable changed i want to log that variables
# to our console 
# useUpdatedLogger.js
```js
import {useEffect} from 'react'
// whatever value changes i want to log back to the console 
export default function useUpdateLogger(value){
    useEffect(() =>{
        console.log(value)
    }, [value])
} 
```
# App.js
```js
import React from 'react'
import useLocalStorage from './useLocalStorage'
import useUpdateLogger from './useUpdateLogger'

export default function App(){
    const [name, setName] = useState('name','')
    useUpdateLogger(name)

    return (
        <input
            type="text"
            value={name}
            onChange={e => setName(e.target.value)}
        />
    )
}
```
