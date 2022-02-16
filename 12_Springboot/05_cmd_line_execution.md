## Intro
- When running from the command-line
- No need to have IDE open/running
- Since we using Spring Boot, the server is embedded in our JAR file
- No need to have separate server installed/running
- Spring Boot apps are self-contained

## Two options for running the app
- Option 1: Use java -jar
- Option 2: Use Spring Boot Maven plugin
    - mvnw spring-boot:run

## Option 1 : Use Java Jar 
`java -jar mycoolapp.jar`

## Option 2: Use Spring Boot Maven plugin
- mvnw allows you to run a Maven project
- No need to have Maven installed or present on your path
- If correct version of Maven is NOT found on your computer
    - Automatically downloads correct version and runs Maven
- Two files are provided
    - mvnw.cmd for MS Windows `>mvnw clean compile test`
    - mvnw.sh for Linux/Mac `$ ./mvnw clean compile test`
- Or there is a tool named mvn , if you have installed that then no need of mvnw `$ mvn clean compile test`

- OR 
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```
- You can also easily run the app
```shell
$ ./mvnw package
$ ./mvnw spring-boot:run
```

## What we tried
```
mvnw package
cd target
java -jar my_cool_app-0.0.1-SNAPSHOT.jar
```
```
mvnw package
mvnw spring-boot:run
```