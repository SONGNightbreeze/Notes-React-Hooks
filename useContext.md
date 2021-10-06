
#### App.js
```js
import React, {useState} from 'react'
import FunctionContextComponent from './FunctionContextComponent'
import ClassContextComponent from './ClassContextComponent'

export const ThemeContext = React.createContext()

export default function App(){
    const [darkTheme, setDarkTheme] = useState(true)

    function toggleTheme(){
        setDarkTheme(prevDarkTheme => !prevDarkTheme)
    }

    return (
        <>
            <ThemeContext.Provider value ={darkTheme}>
                <button onClick={toggleTheme}>Toggle Theme</button>
                // we have two different component
                <FunctionContextComponent />
                <ClassContextComponent />
            </ThemeContext.Provider>
        </>
    )
}
```
> we can compare and contrast the class version and the function version of context
> because they're actually very different
> another important thing to note is that 
> when you're using context it's broken into two different sections
> you have a context provider which is what you want to wrap all of the code
> that needs access to the information in the context
> and it has a single prop called the value which is going to be whatever 
> the value of your context is 
> the important thing about context is everything inside of our provider
> all of their components children all have access to the variable in this value
> prop of our theme context provider
> context is for passing down props essentially all the way down into any of the children
> without actually having to manually pass you  dark theme into the props of each one of 
> these components, it's just available to all of them it's kind of like a global 
> state for all of the children of the provider, rectangle,  

#### ClassContextComponent
```js
import React, {Component} from 'react'
// import the ThemeContext from app here
import { ThemeContext } from './App'

export default class ClassContextComponent extends Component{
    themeStyles(dark){
        return {
            backgroundColor: dark? '#333' : '#CCC',
            color: dark ? '#CCC' : '#333',
            padding: '2rem',
            margin: '2rem'
        }
    }
    render(){
        return(
            <ThemeContext.Consumer>
                // a function that we need to pass here
                {darkTheme =>{
                    return <div style={this.themeStyles(darkTheme)}>Class Theme</div>
                }}
            </ThemeContext.Consumer>
        )
    }
}

```
#### FunctionContextComponent
```js
import React, {useContext} from 'react'
import {ThemeContext} from './App'

export default function FunctionContextComponent(){
    // we can just inline without having to do any wrapping or nesting
    // or function 
    const darkTheme = useContext(ThemeContext) 
    // so we pass in our context to this useContext and it's going to give us
    // the value from that context in our case dark theme
    // and we could just use that inside of any of our code that we want
    const themeStyles = {
            backgroundColor: darkTheme? '#333' : '#CCC',
            color: darkTheme ? '#CCC' : '#333',
            padding: '2rem',
            margin: '2rem'
    }

    return (
        // in order to use a context inside of a function component
        // you can't actually wrap it inside of theme context dot consumer
        // <ThemeContext.Consumer> that is not how you're going to work with this 
        // we need to use instead, is use a hook called the use context
        // import {ThemeContext} from './App'
        <div style={themeStyles}>Function Theme</div>
    )
}
```
-----------------------------------------------------------------------------------------------------------------

#### ThemeContext.js  is going to extract out all of the logic for our theme information
```js
import React, {useContext, useState} from 'react'

const ThemeContext = React.createContext()

export function ThemeProvider({children}){
// this ThemeProvider is going to do, its going to take this theme 
// contact provider in App.js, and that will allow us to take all of this code
// and put it inside of our theme provider which we have right here
    const [darkTheme, setDarkTheme] =useState(true)

    // function toggleTheme(){
    //     setDarkTheme(prevDarkTheme => !prevDarkTheme)
    // }    ignore the toggle theme

    return {
        // ignore the toggle theme, we're passing in the value of dark theme
        // and then we're rendering the children and all of our information
        // for our context is all inside of this one single component
        <ThemeContext.Provider value={darkTheme}>
            {children}
        </ThemeContext.Provider>
    }
}
```
#### so we save this and go into our app instead of having to create our theme here
#### App.js
```js
import React from 'react'
import FunctionContextComponent from './FunctionContextComponent'
import ClassContextComponent from './ClassContextComponent'
import {ThemeProvider} from './ThemeContext'

export const ThemeContext = React.createContext()

export default function App(){
    return (
        <>
            <ThemeProvider>
                <button onClick={toggleTheme}>Toggle Theme</button>
                <FunctionContextComponent />
                <ClassContextComponent />
            </ThemeProvider>
        </>
    )
}
// one problem uou'll notice with this code <button onClick={toggleTheme}>
// doesn't exist yet, so in order to make that toggle theme a reality
// we need to do is inside of our theme context here 
// we need to have another theme context which is going to be for updating 
// our theme
```
#### ThemeContext.js
```js
import React, {useContext, useState} from 'react'

const ThemeContext = React.createContext()
const ThemeUpdataContext = React.createContext()

export function ThemeProvider({children}){

    const [darkTheme, setDarkTheme] =useState(true)

    function toggleTheme(){
        setDarkTheme(prevDarkTheme => !prevDarkTheme)
    }    ignore the toggle theme

    return {
        <ThemeContext.Provider value={darkTheme}>
            <ThemeUpdateContext.Provider value={toggleTheme}>
            </ThemeUpdateContext.Provider>
            {children}
        </ThemeContext.Provider>
    }
}
// so now this ThemeProvider({children}){} it's going to take the children
// so we can wrap it around anything and it's going give us access to both our theme
// as well as function in order to update our theme 
// and then it's just going to render out the children
// so just simplifying all of this code for us into one place
```
> so we can do instead of our app here is 
> remove our class context 

#### App.js
```js
import React from 'react'
import FunctionContextComponent from './FunctionContextComponent'
import {ThemeProvider} from './ThemeContext'

export default function App(){
    return (
        <ThemeProvider>
            <FunctionContextComponent />
        </ThemeProvider>
    )
}
```
#### FunctionContextComponent
```js
import React, {useContext} from 'react'
import {ThemeContext} from './App'

export default function FunctionContextComponent(){

    const darkTheme = useContext(ThemeContext)     
    const themeStyles = {
            backgroundColor: darkTheme? '#333' : '#CCC',
            color: darkTheme ? '#CCC' : '#333',
            padding: '2rem',
            margin: '2rem'
    }

    return (
        <>  
            <button onClick={toggleTheme}>Toggle Theme</button>
            <div style={themeStyles}>Function Theme</div>
        </>
    )
}
```
> how do we actually access our theme provider inside of our function component
> we cant't use this theme context 
#### FunctionContextComponent
```js
import React, {useContext} from 'react'

export default function FunctionContextComponent(){
    // how are we going to use this with useContext 
    const darkTheme = useContext(ThemeContext)     
    const themeStyles = {
            backgroundColor: darkTheme? '#333' : '#CCC',
            color: darkTheme ? '#CCC' : '#333',
            padding: '2rem',
            margin: '2rem'
    }

    return (
        <>  
            <button onClick={toggleTheme}>Toggle Theme</button>
            <div style={themeStyles}>Function Theme</div>
        </>
    )
}
```
> inside of handling the useContext inside of our function component itself
> inside of our theme context we're gonna expose custom hooks
#### ThemeContext.js
```js
import React, {useContext, useState} from 'react'

const ThemeContext = React.createContext()
const ThemeUpdataContext = React.createContext()

// we can create a hook which is going to be called useTheme()
export function useTheme(){
    return useContext(ThemeContext)
}
export function useThemeUpdate(){
    return useContext(ThemeUpdateContext)
}

export function ThemeProvider({children}){

    const [darkTheme, setDarkTheme] =useState(true)

    function toggleTheme(){
        setDarkTheme(prevDarkTheme => !prevDarkTheme)
    }    ignore the toggle theme

    return {
        <ThemeContext.Provider value={darkTheme}>
            <ThemeUpdateContext.Provider value={toggleTheme}>
            </ThemeUpdateContext.Provider>
            {children}
        </ThemeContext.Provider>
    }
}
```
#### FunctionContextComponent
```js
import React from 'react'
import {useTheme, useThemeUpdate} from './ThemeContext'

export default function FunctionContextComponent(){
    // const darkTheme = useContext(ThemeContext)    
    const darkTheme = useTheme()
    const toggleTheme = useThemeUpdate()

    const themeStyles = {
            backgroundColor: darkTheme? '#333' : '#CCC',
            color: darkTheme ? '#CCC' : '#333',
            padding: '2rem',
            margin: '2rem'
    }

    return (
        <>  
            <button onClick={toggleTheme}>Toggle Theme</button>
            <div style={themeStyles}>Function Theme</div>
        </>
    )
}
```

> refresh exactly what's going on so inside of our app
> we have theme provider and this is wrapping all of the logic 
> for handling our state updating our state and pushing out 
> those different values to all of our children
> so inside of our theme you can see we have our theme provider
> which is handling creating our state updating our state 
> and then persisting both of these different values down into our children
> and then we have these two custom hooks (useTheme、TusehemeUpdate)
> which give us easy access to these different values so we have our theme value as well as our theme update value 
> and this is just a really clean way to handle context inside of react
> then inside of your components all you need to do use your custom hooks
> that you created for using the theme or using the theme update function 
> you need to provide the thing you just need this one simple theme provide class
> and we don't have to worry about any of the complexities of handling how react
> does context internally all that is taken care of in this single theme context class
