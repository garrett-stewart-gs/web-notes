# Create, Read, Update, Delete (CRUD)

Create - adds a new record
Read - Retrieves the value of a record
Update - Updates the value of a record
Delete - Deletes a record

Here some JS examples (assuming you are NOT using a database, which we will be doing later on. Databases are the standard, because they persist after the app stops running):


```js
//create
users['5315'] = {first_name:'john', last_name: 'smith'};
```
```js
//read
users['5315']
```
```js
//update
users['5315'].first_name = 'jane';
```
```js
//delete
delete users['5315']
```


Here are the HTTP Method Equivalent:

```
GET = Read
```
```
POST = Create
```
```
PUT = Update
```
```
DELETE = Delete
```

## IDEMPOTENCE AND SAFETY
What is this? 

PUT and DELETE are IDEMPOTENT. If multiple identical PUT and DELETE methods are called, the successive requests will be ignored (ie. they run once)

POST will make changes for successive identical requests.

GET is a safe method, because it has NO INTENDED EFFECT on the server. It is safe because it cannot make changes

