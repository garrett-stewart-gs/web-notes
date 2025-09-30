




Sharing State between 2 components requires that the state is managed in the CLOSEST COMMON ANCESTOR (parent or grandparent, etc..). The state must be passed to the child components that are sharing the same state via the PROPS OBJECT.

The state and the functions that operate on that state MUST BE STORED IN THE SAME PLACE. Never pass the setX (inner function) down to child components through the props object

```js
function App(){

  // state and functions that operate on state are stored TOGETHER in the closest common ancestor
  const [counter,setCounter] = useState(0);
  const increment = ()=>{
    setCounter(prev => prev + 1);
  }

  return (
    <div className="App">

      // CORRECT
      <Counter counter={counter} increment={increment}/> //imported component is being called and passed increment function as an arguement! 

      //INCORRECT
      <Counter counter={counter} setCounter={setCounter}> //NEVER PASS THE INNER WORKINGS OF STATE MANAGEMENT TO THE CHILD (We want smart parents + dumb children)

    </div>
  )

}
```