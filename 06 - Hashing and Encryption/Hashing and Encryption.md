## Hashing
* one-way process for encrypting plain text
* hashing requires advanced math. don't build your own
* bcryptjs is a commonly used hashing function. Needs v12 of node at a minimum (don't use bcrypt, it is built in c++ so it is finicky to use with node)
* it is recommended to experiment with bcrypt before putting it into our server file.


### generate salt 
a salt is a randomly generated string that is added to the password before creating a hash:

password + salt => algo => hash
ex: '1234' + 'o234hfGx3' => algo => 'skd;ldjfha217e9rxHUG3783'

generating a salt is unique every time.


```js
const bcrypt = require('bcryptjs');

//practice syntax, not practical
bcrypt.genSalt(10); //if you pass this a cb function, it doesn't return a promise. If you pass it a value, it does return a promise
  .then((salt) =>{ //.then is working because you are not passing it a callback function
    console.log('salt in promise',salt);
  });


  const salt = bcrypt.genSaltSync(10);
  const plainTextPassword = 'monkeyFuzz';

  const hash = bcrypt.hashSync(plainTextPassword, salt);

  const dbHash = 'als92384G8gf7G872;Ikajf2398h;adf';

  const result = bcrypt.compareSync('monkeyFuzz', dbHash);


```

on registration, generate hash
on login compare password to hash (compare re-hashes... i think)


### Encryption
* 2-way process (similar to hashing, but can be decrypted)
* Use middleware = cookie-session (if you are using cookie-session you cannot use cookie-parser middleware)

plaintextCookie => encryption algo => encryptedCookie
encryptedCookie => decryption algo => plaintextCookie

```js
const cookieSession = require('cookie-session');


//set cookie with
req.session['key'] = value

//read cookie with
req.session['key']

//clear cookies with
req.session['key'] = null //clears specific cookie
req.session = null //clears all cookies
```

you need 2 cookies = cookie.sig and cookie (these are names)


### Asymetric Encryption (https uses this)
* public/private key system
* public key is available to everyone
* private key is stored on the server
* any communication between server/client is encrypted with the public key and decrypted when communication arrives
* private


### REST
* naming convention for routes = Representational State Transfer
* all resources are plural (nouns are plural)
* actions are singular (ie. login, register, etc.)
* you can also nest the REST structure (ie. /orders/:id/products/:id)

Stick to this conventions, because you can quickly create paths or other things (ie. products, dinosaurs, etc.)
* Browse GET (all) /Products
* Read GET (individual) /products/:id
* Edit POST /products/:id
* Add POST /products
* Delete POST /products/:id/delete

### MORE VERBS
* Get & Post are the only things that http understands
* PUT - replace a resource completely
* PATCH - replace a piece of a resource
* DELETE - delete a resource entirely

put, patch, and delete are semantic aliases for POST, they do the same thing (given the code you use is correct). It is like setting a = 5 versus age = 5. They do not do anything without the code that you put into it (ie. no magic inherent to put, patch or delete)