## Add Maven dependencies for Spring Security
```
Spring Security 
- spring-security-web
- spring-security-config
```

## Spring Projects
```
Spring Security                              Spring Framework
                                             (core,aop,mvc...)
```
- These are two separate projects
- The projects are on different release cycles
- Version numbers between projects are generally not in sync (sigh…)
    - Spring team is working to resolve this for future releases…
- Common pitfall is using incompatible projects
- Need to find compatible version

## Finding compatible version
- Find desired version of Spring Security in Maven Central Repo
    `spring-security-web`
- Look at the project POM file
    - Find supporting Spring Framework version

## Updating POM files 
```xml
<properties>
    <springframework.version>5.0.2.RELEASE</springframework.version>
    <springsecurity.version>5.0.0.RELEASE</springsecurity.version>
</properties>
...
<!-- Spring -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>${springframework.version}</version>
</dependency>

<!-- Spring Security -->
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-web</artifactId>
    <version>${springsecurity.version}</version>
</dependency>

<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-config</artifactId>
    <version>${springsecurity.version}</version>
</dependency>
```