## ID GENERATION STRATEGIES 
- We can tell hibernate how to perform Generation of Primary Keys

| Name | Description |
|------|-------------|
|GenerationType.AUTO|Pick an appropriate strategies for the particular database|
|GenerationType.IDENTITY|Assign Primary keys using database identity|
|GenerationType.SEQUENCE|Assign Primary keys using a database sequence|
|GenerationType.TABLE|Assign Primary keys using an underlying database table to ensure uniqueness|

## Defining Custom Strategies 
- You can define your own CUSTOM generation strategies 
- Create implementation of org.hibernate.id.IdentifierGenerator
- Override the method : public Serializable generate(...)


### WARNING!
- Always generate unique values 
- Work in high volume, multithreaded environment 
- If using clusters, always generate unique values. 

## Saving muliple rows at once 

`src/com.luv2code.hibernate.demo`
```Java
package com.luv2code.hibernate.demo;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

import com.luv2code.hibernate.demo.entity.Student;

public class PrimaryKeyDemo {

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
			System.out.println("Creating 3 New Student : ");
			Student tempStudent1 = new Student("John","Doe","john@luv2code.com");
			Student tempStudent2 = new Student("Mary","Public","mary@luv2code.com");
			Student tempStudent3 = new Student("Bonita","Applebum","bonita@luv2code.com");
			// TODO start a transaction 
			session.beginTransaction(); 
			
			// TODO save the student object 
			System.out.println("Saving the object into database");
			session.save(tempStudent1);
			session.save(tempStudent2);
			session.save(tempStudent3);
			
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

## Changing the AUTO_INCREMENT starting index 
- Whatever value we are putting here should be larger than the max value in the database for PRIMARY KEY. 
```sql 
ALTER TABLE hb_student_tracker.student AUTO_INCREMENT = 3000 ; 
```

## Resetting the AUTO_INCREMENT to one 
```
truncate hb_student_tracker.student;
```