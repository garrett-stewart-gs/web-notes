## Procedure
1. launch postgres server
2. connect to postgres server
3. connect to an existing database
4. create table with ```CREATE TABLE table_name (id SERIAL PRIMARY KEY, column_header1 VARCHAR(255) NOT NULL, column_header2 BOOLEAN DEFAULT FALSE);```
5. verify the table has been created with ```\dt``` in command line.