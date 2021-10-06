
> useReducer which also allow you to manage state and re-render a component
> whenever that state changes and idea behind useReducer 
> it give you a more concrete way to handle complex state
> so it give you set actions that you can perform on your state
> it's going to convert your current state to a new version of the state
> based on the action that you send it 
> useReducer is similar to redux

```js
import React, {useState} from 'react'

export default function App(){
    const [count, setCount] = useState(0)

    function increment(){
        setCount(prevCount => prevCount + 1)
    }

    function decrement(){
        setCount(prevCount => prevCount - 1)
    }

    return (
        <>
            <button onClick={decrement}>-</button>
            <span>{count}</span>
            <button onClick={increment}>+</button>
        </>
    )
}
```
#### convert this counter application to useReducer
```js
import React, {useState, useReducer } from 'react'

// this reducer is going to take two different things, it's going to take current state
// when our application si currently and it's going to take an action 
// this action is what we pass into this dispatch function 
// so whenever we call dispatch what's going to happen is whatever we call dispatch
// with is going to get set to this action variable here and then our current state
// is going to be in this state variable and reducer is going 
// to return our new updated state
function reducer(state, action){
    switch (action.type){
        case: 'increment':
            return { count: state.count + 1 }
        case: 'decrement':
            return { count: state.count - 1 }
}

export default function App(){

    // return a array
    const [] = useReducer(reducer, {count: 0})
    // this reducer function is going to take two different parameters 
    // to start we're going to have a reducer which is a function that we perform
    // on our state to get new state , it's going to have a initial value
    // so we're gonna to pass it a function which we call reducer
    // the seconde parameter we're going to pass it our initial state which in our case
    // we're just going to use an object {count: 0}
    // the same as useState(0)

    // return value 'const []' is going to be two portions, the first portion is going to
    // be the state, in our case is {count: 0}
    // the second thing is a function called dispatch and as a dispatch function 
    // is essentially what we call in order to update our state
    // essentially it's goint to call this reducer up here for us give certain parameters


    function increment(){
        // pass a type into your dispatch
        dispatch( {type: 'increment'} )
    }

    function decrement(){
        dispatch( {type: 'decrement'} )
    }

    return (
        <>
            <button onClick={decrement}>-</button>
            <span>{state.count}</span>
            <button onClick={increment}>+</button>
        </>
    )
}
```

```js
import React, {useState, useReducer } from 'react'

// create a constant variable
const ACTIONS = {
    INCREMENT:'increment',
    DECREMENT: 'decrement'
}

function reducer(state, action){
    switch (action.type){
        case: ACTIONS.INCREMENT:
            return { count: state.count + 1 }
        case: ACTIONS.DECREMENT:
            return { count: state.count - 1 }
        default:
            return state
}

export default function App(){

    const [state,dispatch] = useReducer(reducer, {count: 0})

    function increment(){
        dispatch( {type: ACTIONS.INCREMENT} )
    }

    function decrement(){
        dispatch( {type: ACTIONS.DECREMENT} )
    }

    return (
        <>
            <button onClick={decrement}>-</button>
            <span>{state.count}</span>
            <button onClick={increment}>+</button>
        </>
    )
}
```
---------------------------------------------------------------------------------------------------------------------------
> when you're working with more complex state or a lot of nested components that 
> you have to worry about passing props down into 
> example to show you how this can be used instead of a more complex use case 
> and really show you the power of use reducer
#### App.js
```js
import React, {useState, useReducer } from 'react'
import Todo from './Todo.js'

export const ACTIONS = {
    ADD_TODO: 'add-todo',
    TOGGLE_TODO: 'toggle-todo',
    DELETE_TODO: 'delete-todo'
}

function reducer(todos, action){
    switch(action.type){
        case ACTIONS.ADD_TODO:
            return [...todos, newTodo(action.payload.name)]
        case ACTIONS.TOGGLE_TODO: 
            // we need to take all of our current todos
            // and we need to map over them to get a new list of todos
            // and we didn't take the current todo that we have toggled
            // and set that to complete or uncomplete depending on what it is
            return todos.map(todo => {
                // check if our todo id is equal to the current ID
                if(todo.id === action.payload.id){
                    // that case i want to get a new to do that is complete
                    return {...todo, complete: !todo.complete}
                }
                return todo // if it's not equal to the current todo
                            // return todo as is because we dont want to modify it
        // now we can do how to setup this toggle to do we need to pass in to our dispatch
        // function this action as well as a payload 
        // that contains the ID of the current todo 
            })
        case ACTIONS.DELETE_TODO:
            // we use .filter them to remove the to do that we dont want the one 
            // we're trying to delete
            return todos.filter(todo => todo.id !== action.payload.id)
            // if the ID of our todo is not equal to the payload ID then we keep it
            // otherwise we get rid of it so it'll delete whatever todo we want to get rid
        default :
            return todos
    }
}

function newTodo(name){
    return {id: Date.now(), name:name, complete: false }
}

export default function App(){

    const [todos, dispatch] = useReducer(reducer, [])
    const [value, setValue] = useState('')

    function handleSubmit(e){
        // type something hit enter you can see it clears out name
        // but it's refreshing our page
        // let's make sure here, we do e.prevent
        e.preventDefault()

        dispatch({ type: ACTION.ADD_TODO, payload: {name: name}}) 
        // payload is an object which essentially contains all of the variable values
        // we need to perform that action in our case we need to pass in a name
        // set our name back to an empty string 
        setName('')
    }

    return (
        <>
            <form onSubmit={handleSubmit}>
                <input type="text" value={name} onChange={e => setName(e.target.value)} />
            </form>
        
            {todos.map(todo => {
            return  <Todo key={todo.id} todo={todo} dispatch={dispatch}/>
            })}
        </>
    )
}
```
#### Todo.js
```js
import React from 'react'
import {ACTIONS} from './App/js' 

export default function Todo({todo, dispatch}){
    return(
        <div>
            <span style={{color:todo.complete ? '#AAA' :'#000'}}> 
                {todo.name} 
            </span>
            // we're calling an onclick function which calls our dispatch with toggle
            // to do type and it gives us the idea of the current to do 
            // inside of the payload
            <button onClick={() => dispatch({type:ACTIONS.TOGGLE_TODO,payload:{id:todo.id}})}>Toggle</button>
            <button onClick={() => dispatch({type:ACTIONS.DELETE_TODO,payload:{id:todo.id}})}>Delete</button>
        </div>
    )
}

```
