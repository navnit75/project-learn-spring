## The problem
- When running Spring Boot applications
- If you make changes to your source code
- Then you have to manually restart your application :-(

## Solution: Spring Boot Dev Tools
- spring-boot-devtools to the rescue!
- Automatically restarts your application when code is updated
- Simply add the dependency to your POM file
- No need to write additional code :-)
- Adding the dependency to your POM file

```xml
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools<artifactId>
</dependency>
```

## Developement Process
1. Edit pom.xml and add spring-boot-devtools
2. Add new REST endpoint to our app
3. Verify the app is automatically reloaded
4. For more details [Look here](www.luv2code.com/devtools-docs)

