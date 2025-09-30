## Server Actions

### What are they?
* Functions that only run on the server
* Functions must be asynchronous
* Must have the ```"use server";``` directive
* * can be used at beginning of file to ensure all functions contained in that file exectute on the server
* * can be used at the beginning of a function block to ensure all code within that function executes on the server
* cannot use hooks