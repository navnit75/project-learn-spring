## Process
- Create a web app maven project with eclipse
- Explore the generated files and directories
- Add dependencies to our POM files 
- Run our webapp

## Steps to create a web app in maven
![](./images/create_webapp.gif)

## Added Servlet Dependency
- Using GAV
```xml
    <dependency>
	  <groupId>javax.servlet</groupId>
	  <artifactId>javax.servlet-api</artifactId>
	  <version>3.1.0</version>
	</dependency>
```