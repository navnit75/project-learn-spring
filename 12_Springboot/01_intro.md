## To be learnt
Quickly develop Spring Boot applications
- Develop a REST API using Spring Boot
- Create a Spring MVC app with Spring Boot
- Connect Spring Boot apps to a Database for CRUD development
- Leverage all Java configuration (no xml) and Maven

## The Problem
- Building a Spring application is really HARD!!!
    - Q: What Maven archetype to use?
    - Q: Which Maven dependencies do I need?
    - Q: How do I set up configuration (xml or Java)?
    - Q: How do I install the server? (Tomcat, JBoss etc…)
- Tons of configuration : Very error prone(easy to make a simple mistake)

## Spring Boot Solution
- Make it easier to get started with Spring development
- Minimize the amount of manual configuration
- Perform auto-configuration based on props files and JAR classpath
- Help to resolve dependency conflicts (Maven or Gradle)
- Provide an embedded HTTP server so you can get started quickly
- Tomcat, Jetty, Undertow, …

## Spring Initializr
- [spring.io](http://start.spring.io)
- Quickly create a starter Spring project
- Select your dependencies
- Creates a Maven/Gradle project
- Import the project into your IDE
- Eclipse, IntelliJ, NetBeans etc …

## Spring Boot Embedded Server
- Provide an embedded HTTP server so you can get started quickly
- Tomcat, Jetty, Undertow, …
- No need to install a server separately

```
mycoolapp.jar
+------------+      Jar file 
| - my code  |  Includes your application code 
| - tomcat   |          AND 
+------------+      includes the server
```           


## Running Spring Boot Apps
- Spring Boot apps can be run standalone (includes embedded server)
- Run the Spring Boot app from the IDE or command-line
`java -jar mycoolapp.jar`

## Deploying Spring Boot Apps
- Spring Boot apps can also be deployed in the traditional way
- Deploy WAR file to an external server: Tomcat, JBoss, WebSphere etc …

```
+--------------------------------------------------------------+
|Tomcat                                                        |
|     mycoolapp.war         travel.war        shopping.war     |
+--------------------------------------------------------------+

```
## Spring Boot FAQ #1
Q: Does Spring Boot replace Spring MVC, Spring REST etc …?
- No. Instead, Spring Boot actually uses those technologies
```
+----------------------------------+
|           Spring Boot            |
+----------------------------------+
|Spring MVC, Spring REST, Spring … |
|Spring Core, Spring AOP, Spring … |
+----------------------------------+
```

Q: Does Spring Boot run code faster than regular Spring code?
- No.
    - Behind the scenes, Spring Boot uses same code of Spring Framework
    - Remember, Spring Boot is about making it easier to get started
        - Minimizing configuration etc …

Q: Do I need a special IDE for Spring Boot?
- No.
    - You can use any IDE for Spring Boot apps … even use plain text editor
    - The Spring team provides free Spring Tool Suite (STS) [IDE plugins]
    - Some IDEs provide fancy Spring tooling support
    - Not a requirement. Feel free to use the IDE that works best for you