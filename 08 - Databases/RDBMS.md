postgres is a RDBMS = a protocol for managing the sql server/database.
It holds multiple databases

elephant sql = 

## Communication Map (addition = rdbms)
client <===tcp/http===>web server <===tcp/postgres===> rdbms

As you can see postgres sql is actually how the database communicates with the web server

## Teminology
* Rows = 'Records'
* Columns = 'fields'
* relational database = collection of tables, where one or more other tables are related
* Primary Key = unique identifier. EVERY TABLE needs a unique identifier
* Foreign Key = a PRIMARY KEY originating from another table within the database
* Single Source of Truth = STORE DATA IN ONE PLACE ONLY, so that duplicate values don't get mixed up

## Aggregate Commands
COUNT()
AVERAGE()
SUM()
MIN()
MAX()

## Common Functions
NOW() - finds current date/time/hour/minute/second in Universal Time. Can be used to create dynamic query to check for items that are past due among others.

## Common Constants
CURRENT_DATE - has current date MINUS time
CURRENT_TIME - has current time MINUS day/month/year
JOIN - links another table to the query. You need to specify the foreign key with the ON keyword.EVERY 'JOIN' MUST ALSO HAVE AN 'ON' the default join type = INNER. Other types include LEFT OUTER JOIN, RIGHT OUTER JOIN, FULL OUTER, ex:
```sql
JOIN songs ON albums.id = album_id;
```

## Clauses
WHERE - 1st filter. CANNOT have aggregate functions inside it. Can only have 1 per query/statement (not counting sub-queries), allows for AND/OR key words
ORDER BY - Sorts results. Results are naturally un-ordered, we always need to impose order with this clause if we want our results to be ordered. FIELD = ASC or DESC (default = ASC). Add multiple orders with ','. The first order clause is highest priority and it is followed by subsequent sort clauses
HAVING - 2nd filter. can have aggretate functions inside it, OR after it is grouped by
LIMIT - limits the amount of results in the query. LIMIT 10 gives the first 10 result items.
OFFSET - available to limit. it allows limit to skip entries. You can use this for each page of results (ie. pagination) OFFSET = (page number -1) * amount per page

## Common Errors
"" - double quotes are RESERVED for table COLUMNS. You cannot use them for strings. (this way, you can put spaces in column names, but this is not recommended!!)
'' - single quotes can be used for strings!

