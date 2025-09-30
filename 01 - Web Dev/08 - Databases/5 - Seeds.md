## Seeds Overview
In development, you will need to constantly test, update, and reset your database tables/relations. To accomplish this, we use schemas and seeds. 

## Procedure
1. create seed and name it accordingly (ie. ## - table_name.sql)
2. Order the seeds in the same order as the schemas (which is based on the amount of dependencies for each table/relation).
3. AFTER THE SCHEMA FILE IS RUN, Run the seeds against the database with the command ```\i /path/to/seed_file.sql```

## Example Code
Each relation/table gets a schema file structured as follows:
```sql
INSERT INTO table_name (name, email, cohort_id, etc..) VALUES 
('John Doe', 'example@example.com',1),
('Jane Doe', 'example2@example.com',1);
```