## Problem
- You need for your app to be configurable … no hard-coding of values
- You need to read app configuration from a properties file

## Solution: Application Properties file
- By default, Spring Boot reads information from a standard properties file
- Located at: src/main/resources/application.properties
- You can define ANY custom properties in this file
- Your Spring Boot app can access properties using @Value

## Example Development Process
1. Define custom properties in application.properties
**src/main/resources/application.properties**
```
#
# Define custom properties
#
coach.name=Mickey Mouse
team.name=The Mouse Club
```
2. Inject properties into Spring Boot application using @Value
```java
@RestController
public class FunRestController {
    
    // inject properties for: coach.name and team.name
    
    @Value("${coach.name}")
    private String coachName;
    
    @Value("${team.name}")
    private String teamName;
    …
}
```

## Spring Boot Properties
- Spring Boot can be configured in the application.properties file
- Server port, context path, actuator, security etc …
- Spring Boot has 1,000+ properties … wowzers!
- [List of Common properties](www.luv2code.com/spring-boot-props)
- Don’t let the 1,000+ properties overwhelm you
- The properties are roughly grouped into the following categories

|Core| Web| Security| Data|
|----|----|---------|-----|
|**Actuator** |**Integration** |**DevTools** |**Testing**|

## Core Properties
```
# Log levels severity mapping
logging.level.org.springframework=DEBUG
logging.level.org.hibernate=TRACE
logging.level.com.luv2code=INFO

# Log file name, totally optional
logging.file=my-crazy-stuff.log
```
- Logging Levels
```
TRACE
DEBUG
INFO
WARN
ERROR
FATAL
OFF
```

## Web Properties
```
# HTTP server port
server.port=7070

# Context path of the application
server.servlet.context-path=/my-silly-app

# Default HTTP session time out
server.servlet.session.timeout=15m
```

## Actuator Properties
```
# Endpoints to include by name or wildcard
management.endpoints.web.exposure.include=*

# Endpoints to exclude by name or wildcard
management.endpoints.web.exposure.exclude=beans,mapping

# Base path for actuator endpoints
management.endpoints.web.base-path=/actuator
```

## Security Properties
```
# Default user name
spring.security.user.name=admin

# Password for default user
spring.security.user.password=topsecret
```

## Data Properties
```
# JDBC URL of the database
spring.datasource.url=jdbc:mysql://localhost:3306/ecommerce

# Login username of the database
spring.datasource.username=scott

# Login password of the database
spring.datasource.password=tiger
```

- [List of Common Properties](www.luv2code.com/spring-boot-props)