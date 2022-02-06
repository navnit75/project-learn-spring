## Process
- Learn about the Eclipse support for maven
- Create simple maven project and eclipse
- Explore generated files and directories
- Run our app
- Update the Java version settings.

## m2eclipse
- Most recent versions of Eclipse have built in support for maven 
    - Use **m2eclipse** plugin
- There is no need to download/install Maven separately. 
- Can create Maven projects, add dependencies all inside Eclipse. 

## Checking the m2eclipse support 
![](./images/m2e_support.gif)

## Creating a quickstart maven app
![](./images/create_quickstart_maven.gif)

## Updating pom.xml
- Update is done for using Java 1.8 variant
```xml
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.target>1.8</maven.compiler.target>
    <maven.compiler.source>1.8</maven.compiler.source>
  </properties>
```
- Right Click Project --> Maven --> Update Project
![](./images/update_folder.gif)