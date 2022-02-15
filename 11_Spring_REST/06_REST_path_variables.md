## Path Variables
- Retrieve a single student by id
- `GET` **/api/students/{studentId}** 
- **{studentId}** Known as a `path variable`
- Retrieve a single student
```
/api/students/0
/api/students/1
/api/students/2
```


## Behind the Scenes 
![](./images/behind_the_scenes_path.jpg)

## Development Process
1. Add request mapping to Spring REST Service
    - Bind path variable to method parameter using `@PathVariable`

**StudentRestController.java**
```java
@RestController
@RequestMapping("/api")
public class StudentRestController {
    // define endpoint for "/students/{studentId}" - return student at index
    @GetMapping("/students/{studentId}")
    public Student getStudent(@PathVariable int studentId) {
        List<Student> theStudents = new ArrayList<>();
        // populate theStudents
        …
        return theStudents.get(studentId);
    }
}
```
