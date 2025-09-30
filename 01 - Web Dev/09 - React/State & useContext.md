## Use Context

Purpose of React Context is to avoid prop drilling, and meticulously passing data from parent components to child components

### Setup 
1. Import createContext from React
2. Create Context Provider
3. Enclose Components that need shared context/data/state inside the context provider
4. Pass shared context/data/state into context provider via 'value' property
5. Consume Context with useContext Hook inside child components. Pass the DEFINED CONTEXT (where provider is defined), into the useContent hook to retrieve the data you need

### Example Code
```js

// INSIDE src/index.js OR src/main.js

// INSIDE src/app.js

// INSIDE src/hooks/AppContext.js
import {
  useContext, 
  createContext, 
  useState
} from 'react';

const AppContext = createContext();
const SetAppContext = createContext();

// abstracted function for consuming app context (app state)
// *** to be imported into any child component consuming context
export function useAppContext(){
  return useContext(AppContext);
}

// abstracted function for consuming app setter context (app state setter)
// *** to be imported into any child component consuming context
export function updateAppContext(){
  return useContext(SetAppContext);
}

// abstracted app context provider
// *** to be imported into any parent component establishing context provider
// children = anything inside AppProvider Component Tags (located inside src/index.js OR src/main.js)
// using children allows you to wrap whatever is passed as a child inside of the non-abstracted context.provider!!
export function AppProvider({children}){
  // can set state here (useState or useReducer)
  const [appData, setAppData] = useState({});

  // abstracted function for updating appData, by calling setAppData
  function updateAppData(){
    setAppData();
  }

  return (
    // pass state to AppContext provider using 'value' property
    // pass state-setter to SetAppContext provider using 'value' property
    // wrap children inside nested context providers 
    <AppContext.Provider value={appData}> 
      <SetAppContext.Provider value={updateAppData}>
        {children}
      </SetAppContext.Provider>
    </AppContext.Provider>
  )
}

```
