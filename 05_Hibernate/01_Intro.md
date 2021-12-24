## What is hibernate? 
- A framework for persisting / saving Java objects in a database. 
- [www.hibernate.org](www.hibernate.org)
```

+--------------+      +-------------+        +----------+
|   Your Java  |----->|  Hibernate  |------->| Database |
|      App     |<-----|             |<-------|          |
+--------------+      +-------------+        +----------+

```

## Object to Realational Mapping (ORM)
- The developer defines mapping between Java class and database table. 
```
    Java Class                                            Database Table 
+----------------------+                                +-----------------------+
|       Student        |                                | student               |
+----------------------+        +---------------+       +-----------------------+
| - id : int           |------->|   Hibernate   |------>| id INT                |
| - firstName: String  |<-------|               |<------| first_name VARCHAR(45)|
| - lastName : String  |        +---------------+       | last_name VARCHAR(45) |
| - email : String     |                                | email VARCHAR(45)     |
+----------------------+                                +-----------------------+
```
