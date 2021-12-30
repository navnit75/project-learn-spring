## Primary Key and Foreign Key 
- Primary Key : Identify a unique row in a table
- Foreign Key: 
    - Link Tables Together 
    - a field in one table that referes to primary key in another table
    - main purpose is to preserve relationship between tables. 
        - Referential Integrity
    - Prevents operations that would destroy relationships. 
    - Ensures only valid data into foreign key coloumns. 
        - Can only obtain valid reference to primary key in other table. 
## Cascade
- You can cascade operation 
- i.e Apply the same operation to the related entities. 
```
+---------------+              +-------------------+
|   Instructor  |------------->| Instructor Detail |
+---------------+              +-------------------+
     SAVE----------CASCADE----------->SAVE
```
- If we delete an instructor, we should also delete their instructor_detail
    - This is known as "CASCADE DELETE"
- But in  MANY --> MANY mapping. 
- On deletion of one Instructor we donot want the other 

## Fetch Types
- When we fetch / retrieve data, should we retrieve everything? 
    - `Eager` will request everything
    - Ex. If we have 
    ```
    Instructor ----> Courses(Multiple). 
    ```
    - Eager will retrieve courses as well on one shot . 
    - `Lazy` will retrieve on request. 




## Uni Directional Data retrival 
```
Instructor ------> Instructor_Detail
```
- Trying to get the instructor detail using Instructor object. 


## Bi Directional 
- Retrieving Instructor detail with the help of Instructor object. 
``` 
Intructor <-------> Instructor_Detail 
```
- Meanwhile also retrieving the Instructor , with the help of data in Intstructor_Detail relations.