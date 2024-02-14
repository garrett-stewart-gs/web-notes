## Getting Started
* postgres is a SERVER on the local machine that can host DATABASES. You create databases on that server. Each database contains relations (tables). 
1. Start the Server with: ```startpostgres```
2. Authorize server start with Labber PW: 'labber'
3. Create or Connect to the database
4. Create Relation Tables
5. Query Database

** Note: all postgre sql commands need include a ';' to end the command **

## Common Commands
CREATE DATABASE -namehere-; //Creates new DB
\c -DB Name here- //connects to existing DB
CREATE TABLE (this command must be written across several lines, the command is not executed until a ';' is used) ex:
  test_db=# CREATE TABLE famous_people (
  test_db(# id SERIAL PRIMARY KEY,
  test_db(# first_name VARCHAR(50),
  test_db(# last_name VARCHAR(50),
  test_db(# birthdate DATE);
  CREATE TABLE