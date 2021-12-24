## There are two ways to udpate DB
- Using setters property 
- Using the Query 

## Implementation

`/com.luv2code.hibernate.demo/UpdateStudentDemo.java`
```Java
package com.luv2code.hibernate.demo;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

import com.luv2code.hibernate.demo.entity.Student;


public class UpdateStudentDemo {

	public static void main(String[] args) {
		// TODO Create session method 
				SessionFactory factory = new Configuration()
										.configure("hibernate.cfg.xml")
										.addAnnotatedClass(Student.class)
										.buildSessionFactory(); 
				
				// TODO Create session 
				Session session = factory.getCurrentSession();
				
				try {
					
					int studentId = 1;
					
					// start transaction 
					session.beginTransaction(); 
					
					// Retrieve the student the based on id : primary key 
					System.out.println("\nGetting the student with id : "+studentId);
					Student myStudent = session.get(Student.class,studentId);
					
					// updating object 
					System.out.println("Updating student..");

                    //1. Type 
					myStudent.setFirstName("Scooby");

					// commit the transaction 
					session.getTransaction().commit(); 
					
					
					// NEW SESSION 
                    //2. Type
					session = factory.getCurrentSession(); 
					session.beginTransaction(); 
					
					// update email for all students 
					session.createQuery("update Student set email ='foo@gmail.com'").executeUpdate();
					
					session.getTransaction().commit();
					System.out.println("Done!");
				}
				finally {
					factory.close();
				}
	}

}
```