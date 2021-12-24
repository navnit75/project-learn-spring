## Retriving data from database using Primary Key  

`src/com.luv2code.hibernate.demo/ReadStudentDemo.java`
```Java
package com.luv2code.hibernate.demo;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

import com.luv2code.hibernate.demo.entity.Student;


public class ReadStudentDemo {

	public static void main(String[] args) {
		// TODO Create session method 
				SessionFactory factory = new Configuration()
										.configure("hibernate.cfg.xml")
										.addAnnotatedClass(Student.class)
										.buildSessionFactory(); 
				
				// TODO Create session 
				Session session = factory.getCurrentSession();
				
				try {
					
					// TODO create 3 student object 
					System.out.println("Creating a New Student : ");
					Student tempStudent1 = new Student("Daffy","Duck","daffy@luv2code.com");
					
					// TODO start a transaction 
					session.beginTransaction(); 
					
					// TODO save the student object 
					System.out.println("Saving the object into database");
					session.save(tempStudent1);
					
					
					// TODO commit the student object 
					session.getTransaction().commit(); 
					
					
					// My new code
					
					// find out the new student id 
					System.out.println("Saved student. Generated ID : "+tempStudent1.getId());
					
					// now get a new session 
					session = factory.getCurrentSession();
					
					// start a transaction 
					session.beginTransaction();
					
					// retrieve student based on id: Primary Key 
					System.out.println("\nGetting student with id: "+ tempStudent1.getId());
					
					// if the primary key value is not found session.get returns NULL
					Student myStudent = session.get(Student.class, tempStudent1.getId());
					System.out.println("Get Student Complete: "+myStudent);
					

					
					// commit the transaction 
					session.getTransaction().commit(); 
					System.out.println("Done!");
				}
				finally {
					factory.close();
				}
	}

}

```

## HQL
- Query language for retrieving objects. 
- Similar in nature to SQL 
```
where,like,order by,join,in,etc...
```

## Retrieving students 
###  Retrieving all students 
- Caution: We need to use Java Class Name. 
- Here from instance `from Student` --> `Student` is class name.  
      
```Java
List<Student> theStudents = 
                            session
                            .createQuery("from Student")
                            .getResultList();
```

### lastName = "Doe"
- Caution: We need to use Java property name. 
- Here from instance `where s.lastName` --> `lastName` is object `s` property. 
```Java
List<Student> theStudents = 
                            session
                            .createQuery("from Student s where s.lastName = 'Doe')
                            .getResultList();
```

### OR predicate 

```Java
List<Student> theStudents = 
                            session
                            .createQuery("from Student s where s.lastName = 'Doe' OR s.lastName='Daffy'")
                            .getResultList();
```

### LIKE predicate 
```Java
List<Student> theStudents = 
                            session
                            .createQuery("from Student s where s.email LIKE '%luv2cose.com'")
                            .getResultList();
```

`NOTE` : If you are using Hibernate 5.2 or higher, then the Query list() method has been deprecated.

Replace
```Java
session.createQuery("from Student").list()
```
With
```Java
session.createQuery("from Student").getResultList()
```

## CODE implementation
`src/com.luv2code.hibernate.demo/QueryStudentDemo.java`
```Java
package com.luv2code.hibernate.demo;



import java.util.List;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

import com.luv2code.hibernate.demo.entity.Student;


public class QueryStudentDemo {

	public static void displayStudents(List<Student> theStudents ) {
		// display the students 
		for(Student temp: theStudents) {
					System.out.println(temp);
		}
	}
	public static void main(String[] args) {
		// TODO Create session method 
		SessionFactory factory = new Configuration()
								.configure("hibernate.cfg.xml")
								.addAnnotatedClass(Student.class)
								.buildSessionFactory(); 
		
		// TODO Create session 
		Session session = factory.getCurrentSession();
		
		try {
			
			
			
			// TODO start a transaction 
			session.beginTransaction(); 
			
			// query the student
			List<Student> theStudents = session.createQuery("from Student").getResultList();
			
			/*
			 * // query the student using .list() also works
			 * List<Student> theStudents = session.createQuery("from Student").list();
			 */
			
			displayStudents(theStudents);
			
			
			// query students: lastName = "Doe"
			System.out.println("\nStudents who have last name as Doe");
			theStudents = session.createQuery("from Student s where s.lastName='Doe'").list();
			displayStudents(theStudents);
			
			
			// query students: lastName = 'Doe' OR firstName = 'Daffy'
			System.out.println("\n Students who have last name as Doe or Daffy");
			theStudents = session.createQuery("from Student s where s.lastName='Doe' OR s.firstName='Daffy'").list();
			displayStudents(theStudents);
			
			// query email like %luv2code.com
			System.out.println("\n Students who have email ending with luv2code.com");
			theStudents = session.createQuery("from Student s where s.email like '%luv2code.com'").list();
			displayStudents(theStudents);
			
			// TODO commit the student object 
			session.getTransaction().commit(); 
			System.out.println("Done!");
		}
		finally {
			factory.close();
		}

	}

}

```

`Output`
```
Hibernate: select student0_.id as id1_0_, student0_.email as email2_0_, student0_.first_name as first_na3_0_, student0_.last_name as last_nam4_0_ from student student0_
Student [id=1, firstName=John, lastName=Doe, email=john@luv2code.com, getId()=1, getFirstName()=John, getLastName()=Doe, getEmail()=john@luv2code.com]
Student [id=2, firstName=Mary, lastName=Public, email=mary@luv2code.com, getId()=2, getFirstName()=Mary, getLastName()=Public, getEmail()=mary@luv2code.com]
Student [id=3, firstName=Bonita, lastName=Applebum, email=bonita@luv2code.com, getId()=3, getFirstName()=Bonita, getLastName()=Applebum, getEmail()=bonita@luv2code.com]
Student [id=4, firstName=John, lastName=Doe, email=john@luv2code.com, getId()=4, getFirstName()=John, getLastName()=Doe, getEmail()=john@luv2code.com]
Student [id=5, firstName=Mary, lastName=Public, email=mary@luv2code.com, getId()=5, getFirstName()=Mary, getLastName()=Public, getEmail()=mary@luv2code.com]
Student [id=6, firstName=Bonita, lastName=Applebum, email=bonita@luv2code.com, getId()=6, getFirstName()=Bonita, getLastName()=Applebum, getEmail()=bonita@luv2code.com]
Student [id=7, firstName=Daffy, lastName=Duck, email=daffy@luv2code.com, getId()=7, getFirstName()=Daffy, getLastName()=Duck, getEmail()=daffy@luv2code.com]
Student [id=8, firstName=Daffy, lastName=Duck, email=daffy@luv2code.com, getId()=8, getFirstName()=Daffy, getLastName()=Duck, getEmail()=daffy@luv2code.com]

Students who have last name as Doe
Hibernate: select student0_.id as id1_0_, student0_.email as email2_0_, student0_.first_name as first_na3_0_, student0_.last_name as last_nam4_0_ from student student0_ where student0_.last_name='Doe'
Student [id=1, firstName=John, lastName=Doe, email=john@luv2code.com, getId()=1, getFirstName()=John, getLastName()=Doe, getEmail()=john@luv2code.com]
Student [id=4, firstName=John, lastName=Doe, email=john@luv2code.com, getId()=4, getFirstName()=John, getLastName()=Doe, getEmail()=john@luv2code.com]

 Students who have last name as Doe or Daffy
Hibernate: select student0_.id as id1_0_, student0_.email as email2_0_, student0_.first_name as first_na3_0_, student0_.last_name as last_nam4_0_ from student student0_ where student0_.last_name='Doe' or student0_.first_name='Daffy'
Student [id=1, firstName=John, lastName=Doe, email=john@luv2code.com, getId()=1, getFirstName()=John, getLastName()=Doe, getEmail()=john@luv2code.com]
Student [id=4, firstName=John, lastName=Doe, email=john@luv2code.com, getId()=4, getFirstName()=John, getLastName()=Doe, getEmail()=john@luv2code.com]
Student [id=7, firstName=Daffy, lastName=Duck, email=daffy@luv2code.com, getId()=7, getFirstName()=Daffy, getLastName()=Duck, getEmail()=daffy@luv2code.com]
Student [id=8, firstName=Daffy, lastName=Duck, email=daffy@luv2code.com, getId()=8, getFirstName()=Daffy, getLastName()=Duck, getEmail()=daffy@luv2code.com]

 Students who have email ending with luv2code.com
Hibernate: select student0_.id as id1_0_, student0_.email as email2_0_, student0_.first_name as first_na3_0_, student0_.last_name as last_nam4_0_ from student student0_ where student0_.email like '%luv2code.com'
Student [id=1, firstName=John, lastName=Doe, email=john@luv2code.com, getId()=1, getFirstName()=John, getLastName()=Doe, getEmail()=john@luv2code.com]
Student [id=2, firstName=Mary, lastName=Public, email=mary@luv2code.com, getId()=2, getFirstName()=Mary, getLastName()=Public, getEmail()=mary@luv2code.com]
Student [id=3, firstName=Bonita, lastName=Applebum, email=bonita@luv2code.com, getId()=3, getFirstName()=Bonita, getLastName()=Applebum, getEmail()=bonita@luv2code.com]
Student [id=4, firstName=John, lastName=Doe, email=john@luv2code.com, getId()=4, getFirstName()=John, getLastName()=Doe, getEmail()=john@luv2code.com]
Student [id=5, firstName=Mary, lastName=Public, email=mary@luv2code.com, getId()=5, getFirstName()=Mary, getLastName()=Public, getEmail()=mary@luv2code.com]
Student [id=6, firstName=Bonita, lastName=Applebum, email=bonita@luv2code.com, getId()=6, getFirstName()=Bonita, getLastName()=Applebum, getEmail()=bonita@luv2code.com]
Student [id=7, firstName=Daffy, lastName=Duck, email=daffy@luv2code.com, getId()=7, getFirstName()=Daffy, getLastName()=Duck, getEmail()=daffy@luv2code.com]
Student [id=8, firstName=Daffy, lastName=Duck, email=daffy@luv2code.com, getId()=8, getFirstName()=Daffy, getLastName()=Duck, getEmail()=daffy@luv2code.com]
Done!
```

## Logging The inserted data 
- When using Hibernate, if you log the Hibernate SQL statements, you will see this:
- Hibernate: insert into student (email, first_name, last_name, id) values (?, ?, ?, ?)
- However, for debugging your application, you want to see the actual parameter values in the Hibernate logs. Basically, you want to get rid  of the question marks in the Hibernate logs.
- To view parameter values, we can add a logging framework to our project. For this example, we will make use of the Logback project. Logback does not use any log4j source code. Logback is a separate and independent project.

### Here is an overview of the process:
1. Add Logback support to your project classpath
2. Create Logback configuration file
3. Run the CreateStudentDemo app

### Here are the detailed steps:
1. Add Logback support to your project classpath
You have the option of adding logback JAR files manually or using Maven dependencies. Both options are shown below:

Option 1: Download logback JAR files manually
- [logback-core-1.2.7.jar](https://repo1.maven.org/maven2/ch/qos/logback/logback-core/1.2.7/logback-core-1.2.7.jar)
- [logback-classic-1.2.7.jar](https://repo1.maven.org/maven2/ch/qos/logback/logback-classic/1.2.7/logback-classic-1.2.7.jar)
- [slf4j-api-1.7.32.jar](https://repo1.maven.org/maven2/org/slf4j/slf4j-api/1.7.32/slf4j-api-1.7.32.jar)

1b. Copy These files to your project's lib directory. 
1c. Build Path and Jars to class path . 

Option 2:Maven dependencies (only required if using Maven). Add the following dependecy to your Maven pom.xml
```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.7</version>
</dependency>
```
2. Create a Logback configuration file
Notes regarding file location:
- If you are using manual JAR files place this file at 
`src/logback.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
 
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%-5level %logger{0} - %msg%n</pattern>
        </encoder>
    </appender>
 
    <logger name="com.luv2code.hibernate.demo" level="TRACE"/>
    <logger name="org.hibernate.SQL" level="DEBUG" />
    <logger name="org.hibernate.type" level="TRACE" />
 
    <root level="info">
        <appender-ref ref="console"/>
    </root>
 
</configuration>
```

- If you are using Maven, place this file at 
`src/main/resources/logback.xml`

- Please make note of the following entry:
```xml
    <logger name="com.luv2code.hibernate.demo" level="TRACE"/>
```
- Be sure the package name matches the name of your package.
- This Logback configuration file allows you to see a low-level trace of Hibernate and this allows you see the real SQL parameter values.

3. Run the CreateStudentDemo application
- Now run your application. You will see a lot of low-level TRACE logs in the Eclipse Console window.
- You will see the logs with the real parameter values.
```
...
Creating new student object...
Saving the student...
DEBUG SQL - insert into student (email, first_name, last_name) values (?, ?, ?)
Hibernate: insert into student (email, first_name, last_name) values (?, ?, ?)
TRACE BasicBinder - binding parameter [1] as [VARCHAR] - [paul@luv2code.com]
TRACE BasicBinder - binding parameter [2] as [VARCHAR] - [Paul]
TRACE BasicBinder - binding parameter [3] as [VARCHAR] - [Doe]
Done!
...
```