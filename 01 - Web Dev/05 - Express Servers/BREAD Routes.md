Hash Passwords, because you are storing an encryption key on your server. If someone hacks your server, encrypting the passwords offers no defence because the hackers have the encryption key!

Remember encryption is reversible. Hashing isn't...

To compare if a password is correct, you need to compare hashes. If the user entered the correct password, it will produce the same hash.

You need to add a 'salt' into the mix as well, so that hackers do not just hash the most common passwords and look for a matching hash. (if a hacker hashes the same password that we hash on our server, both hashes become identical, so we should add a salt to make it harder to compare our hash with a hacker's hash.)

GET /users

    Retrieves the collection of Users
    Renders a representation of the collection

POST /users

    Creates a new User using the provided request body
    Redirects to a route where the new user can be viewed

GET /users/:id

    Retrieves the User with the specified id
    Renders a representation of the User
    Responds with a 404 - Not Found error if the User doesn't exist

PUT /users/:id

    Updates a specific User using the provided request body
    May redirect to or render a representation of the User
    Responds with a 404 - Not Found error if the User doesn't exist

DELETE /users/:id

    Deletes a specific User
    May redirect to the index of the collection
    Responds with a 404 - Not Found error if the User doesn't exist

More Routes

There are two extra routes that are typical for applications that interact using HTML. These are routes that provide HTML web pages that contain forms that a user can use to interact with the "create resource" and "edit resource" routes. These are both GET routes because they simply return HTML pages.

GET /users/new

    Renders a form that collects information about a new User
    The form submits a POST request to the collection path /users

GET /users/:id/edit

    Renders a form that collects information for altering a User
    The form submits a PUT request to the user's path /users/:id
    Responds with a 404 - Not Found error if the User doesn't exist
