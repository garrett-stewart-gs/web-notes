## Procedure
1. Launch postgres server with ```startpostgres``` command
2. Authorize server launch with password (in my case it is 'labber')
3. Log into serve with command ```psql``` command (may need to log in to or create psql account. In my case password is 'labber' again)
4. Create the database with ```CREATE DATABASE database_name;```
5. Verify the database was created with ```\l``` or ```\list``` (this will show ALL databases on the server)
6. Connect to your newly created database with ```\c database_name```

## Example Code
Inside the command line type:
1. ```startpostgres```
2. ```psql```
3. ```CREATE DATABASE example_db;```
4. ```\l```
5. ```\c example_db```