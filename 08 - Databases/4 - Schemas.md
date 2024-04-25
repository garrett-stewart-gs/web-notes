## Schemas Overview
In development, you will need to constantly test, update, and reset your database tables/relations. To accomplish this, we use schemas and seeds. 

## Procedure
1. create schema and name it accordingly (ie. ## - table_name.sql)
2. Order the schemas based on dependencies. Tables/Relations with the least dependencies go first (ie. they don't reference to other tables/relations)
3. Run the schemas against the database with the command ```\i /path/to/schema_file.sql```

## Example Code
Each relation/table gets a schema file structured as follows:
```sql
DROP TABLE IF EXISTS table_name CASCADE; --before re-creating a table, delete that table if it is already created

CREATE TABLE table_name (
  id SERIAL PRIMARY KEY,
  name VARCHAR(50) NOT NULL,
  email VARCHAR(50) NOT NULL,
  cohort_id INTEGER REFERENCES cohorts(id) ON DELETE CASCADE
  -- ETC....
);
```