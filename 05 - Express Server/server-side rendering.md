when you use javascript to post elements into a page client-side.

templates/variables    =>   html    =>    browser
  (via res.render)    (template output)

### Example Code Using EJS

```js
app.get("/hello", (req, res) => {
  const templateVars = { greeting: "Hello World!" };
  res.render("hello_world", templateVars);
});
```

In the example above, the templateVars object contains the string 'Hello World' under the key greeting. We then pass the templateVars object to the template called hello_world.

In our hello_world.ejs file, we can display the 'Hello World!' string stored in the templateVars object by calling the key greeting:

```html
<!-- This would display the string "Hello World!" -->
<h1><%= greeting %></h1>
```
By using the <%= %> syntax, we tell EJS that we want the result of this code to show up on our page