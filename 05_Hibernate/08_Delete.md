## Two ways
- With the help of session 
- With the help of query 


`src/com.luv2code.hibernate.demo/DeleteUpdateDemo.java`
```Java
package com.luv2code.hibernate.demo;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

import com.luv2code.hibernate.demo.entity.Student;

public class DeleteUpdateDemo {

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
			
			// delete the student
            // 1. Type First
			System.out.println("Delete Student : " + myStudent);
			session.delete(myStudent);
			
			// delete the student using alternate approach 
			System.out.println("Deleting student id = 2");
			
            // 2. Type Second
			// .executeUdpate is used for both UPDATE as well as for DELETE
			session.createQuery("delete from Student where id=2").executeUpdate();

			// commit the transaction 
			session.getTransaction().commit(); 
			
			
		}
		finally {
			factory.close();
		}

	}

}
```