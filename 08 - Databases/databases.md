## Entity Relationship Database (ERD)

Composed of the following parts:
- Entity = person, place, or thing that you want to track in the database
- Attribute = Describes the entities (ie. name, address, etc.)
- Primary Keys = An attribue (or group of attributes) that uniquely identifies an instance of an entity. A primary key MUST BE UNIQUE
- Relationships = descibes how one or more entities interact with eachother. A verb is often used to describe the relationship
- Cardinality = the count of instances that are allowed or necessary between entity relationships. (ie. )

Rows = data
Columns = attributes/properties

## Starting a Database with SQL
```sql
CREATE TABLE groceries (id INTEGER PRIMARY KEY, name TEXT, quantity INTEGER); --sql commands and key words need to be all CAPS. also each argument creates a COLUMN in the database and needs to have a declared data type (ie. INTEGER, TEXT) Also, we are setting the 'id' as the primary key, which is how this individual table will be related to other tables in the database. Primary Keys are usually passed in as an argument first.

-- each item must be individually added to groceries using the INSERT keyword. the first parameter must be a unique integer. the second must be a string of text, and the third must be an integer.
INSERT INTO groceries VALUES (1,'Bananas', 4);
INSERT INTO groceries VALUES (2,'Peanut Butter', 1);
INSERT INTO groceries VALUES (3,'Dark chocolate bars', 2)

-- to retrieve the data from groceries, you must use the SELECT keyword. the '*' means retrieve all rows/datapoints from the table. FROM paired with the table name gives the SELECT keyword/function a target to read data from.
SELECT * FROM groceries;

```

## Common Commands
GROUP BY
HAVING = accessible for grouped data (ie. works with GROUP BY)
DISTINCT = unique entries only
AS = renames selected data as a column name. Instead of SUM(books), you could say SELECT sum(books) AS total_books, and a column called total_books would be created in the query results.
CASE = Creates new column with values that can be determined by conditional logic. ex:
```sql
  SELECT COUNT(*),
    CASE 
        WHEN heart_rate > 220-30 THEN "above max"
        WHEN heart_rate > ROUND(0.90 * (220-30)) THEN "above target"
        WHEN heart_rate > ROUND(0.50 * (220-30)) THEN "within target"
        ELSE "below target"
    END as "hr_zone"
  FROM exercise_logs
  GROUP BY hr_zone;
```

