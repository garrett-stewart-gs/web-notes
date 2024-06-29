## Use Context

Purpose of React Context is to avoid prop drilling, and meticulously passing data from parent components to child components

### Setup 
1. Import createContext from React
2. Create Context Provider
3. Enclose Components that need shared context/data/state inside the context provider
4. Pass shared context/data/state into context provider via 'value' property
5. Consume Context with useContext Hook inside child components. Pass the DEFINED CONTEXT (where provider is defined), into the useContent hook to retrieve the data you need
