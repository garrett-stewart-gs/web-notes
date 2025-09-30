JSX - a combination of javascript and html. It is actually the TEMPLATE LANGUAGE for react (similar to ejs view engine)

Component - a FUNCTION that returns JSX. Components can contain components, which can also contain components. This means React Components are HIGHLY MODULAR

```js
//Here is an example of typical component usage

import Title from './components/Title' //.js or .jsx file extension is implied

function App() {
  return (
    <div className="App">
      <Title/> // FUNCTION CALL that implants the Title component (imported above) into the jsx that is returned by the App Component.
    </div>
  )
}

export default App; //exports the App Component, which is imported by the index.js file (pre-built by running npm create-react-app)

//** NOTE ** any component you use, must be exported before it can be imported. The 'Title' component would need to be exported from its file, so that it could be imported and used in this 'App' component. Then 'App' must be exported so that index.js can import 'App'
```


```js
//here is an example of 2 components within the same file
//This proves that react components are just FUNCTIONS!

const Title = function (text1){ //parameter would need to be called props if this component is called with jsx syntax (<Title/>)
  console.log('props',props);// props {variable: "Howdy"}
  return (
    <>
      <h2>Hello World</h2>
      <h2>{text1}</h2>
      <h2>{props.variable}</h2> //props is an object, so you use dot notation to retrieve a specific value
    </>
  )
}

const App = function (){
  return (
    <div className="App">
      <Title/> //this implants the title's jsx/html in the same way as the line below
      {Title()} //this implants the title's jsx/html in the same way as the line above
      <Title variable="Howdy"/> // you can pass arguments to the specified component. Arguements are passed inside an OBJECT, usually called "props"
      {Title('Hey')} //you can pass arguments to another component like this, but this is not the standard way
    </div>
  )
}
```

