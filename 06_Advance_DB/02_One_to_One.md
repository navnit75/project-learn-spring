## High Level View
```
Instructor ------> instructor_detail
```
- An instructor can have an "instructor detail" entity. 
    - Similar to an instructor profile.
    - This example is UNI-DIRECTIONAL 

## Process 
- Prep Work - Define database tables
- Create Instructor Detail class 
- Create Instructor class
- Create Main app


## Entity Life Cycle
| Operations | Description |
|------------|-------------|
| Detach | If entity is detached, it is not assosiated with a Hibernate Session |
| Merge | If instance is detached from session, then merge will reattach to session. |
| Persist | Transitions new instance to managed state. Next flush / commit will save in db. |
| Remove | Transistions managed entity to be removed. Next flush / commit will delete from db. |
| Refresh | Reload/ synch object with data from db. Prevents stale data. |


![GIF here](./VE_Project_1.gif)
