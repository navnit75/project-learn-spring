## Intro
- One instructor can have many courses. 
- Many couses can have single instructor 

## Requirement 
- If you delete an instructor, DO NOT delete course. 
- If you delete a course, DO NOT delete the instructor. 

## Developement Process
- Prep Work - Define database table

```SQL
DROP SCHEMA IF EXISTS `hb-03-one-to-many`;

CREATE SCHEMA `hb-03-one-to-many`;

use `hb-03-one-to-many`;

SET FOREIGN_KEY_CHECKS = 0;

DROP TABLE IF EXISTS `instructor_detail`;

CREATE TABLE `instructor_detail` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `youtube_channel` varchar(128) DEFAULT NULL,
  `hobby` varchar(45) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=latin1;


DROP TABLE IF EXISTS `instructor`;

CREATE TABLE `instructor` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `first_name` varchar(45) DEFAULT NULL,
  `last_name` varchar(45) DEFAULT NULL,
  `email` varchar(45) DEFAULT NULL,
  `instructor_detail_id` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `FK_DETAIL_idx` (`instructor_detail_id`),
  CONSTRAINT `FK_DETAIL` FOREIGN KEY (`instructor_detail_id`) 
  REFERENCES `instructor_detail` (`id`) ON DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=latin1;

DROP TABLE IF EXISTS `course`;

CREATE TABLE `course` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `title` varchar(128) DEFAULT NULL,
  `instructor_id` int(11) DEFAULT NULL,
  
  PRIMARY KEY (`id`),
  
  UNIQUE KEY `TITLE_UNIQUE` (`title`),
  
  KEY `FK_INSTRUCTOR_idx` (`instructor_id`),
  
  CONSTRAINT `FK_INSTRUCTOR` 
  FOREIGN KEY (`instructor_id`) 
  REFERENCES `instructor` (`id`) 
  
  ON DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB AUTO_INCREMENT=10 DEFAULT CHARSET=latin1;


SET FOREIGN_KEY_CHECKS = 1;

```
1. Create New Project itself. 
`/hb-03-one-to-many`
`/src/hibernate.cfg.xml`
```xml
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

<hibernate-configuration>

    <session-factory>

        <!-- JDBC Database connection settings -->
        <property name="connection.driver_class">com.mysql.cj.jdbc.Driver</property>
        <property name="connection.url">jdbc:mysql://localhost:3306/hb-03-one-to-many?useSSL=false&amp;serverTimezone=UTC</property>
        <property name="connection.username">hbstudent</property>
        <property name="connection.password">hbstudent</property>

        <!-- JDBC connection pool settings ... using built-in test pool -->
        <property name="connection.pool_size">1</property>

        <!-- Select our SQL dialect -->
        <property name="dialect">org.hibernate.dialect.MySQLDialect</property>

        <!-- Echo the SQL to stdout -->
        <property name="show_sql">true</property>

		<!-- Set the current session context -->
		<property name="current_session_context_class">thread</property>
 
    </session-factory>

</hibernate-configuration>
```
2. Create Course Class
`/src/com.luv2code.hibernate.demo.entity/Course.java`
```Java
package com.luv2code.hibernate.demo.entity;

import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.ManyToOne;
import javax.persistence.Table;

@Entity
@Table(name="course")
public class Course {
	// define our fields 
	// define constructors 
	// define getters and setters
	// define to string 
	// annotate fields 
	
	
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	@Column(name="id")
	private int id; 
	
	@Column(name="title")
	private String title; 
	
	// Note: This came in here we are trying to establish a join operation with the 
	// Instructor class. 
	
	// Note: Here the instructor_id is column name i.e of TABLE named course.
	// This would give hibernate the idea regarding the attribute to check at the instructor side. 
	@ManyToOne(cascade={CascadeType.PERSIST,CascadeType.MERGE,
						CascadeType.DETACH,CascadeType.REFRESH})
	@JoinColumn(name="instructor_id")
	private Instructor instructor; 
	
	
	public Course() {
		
	}

	public Course(String title) {
		this.title = title;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public Instructor getInstructor() {
		return instructor;
	}

	public void setInstructor(Instructor instructor) {
		this.instructor = instructor;
	}

	@Override
	public String toString() {
		return "Course [id=" + id + ", title=" + title + "]";
	}

}

```
3. Update instructor class
`/src/com.luv2code.hibernate.demo.entity/Instructor.java`
```Java
package com.luv2code.hibernate.demo.entity;

import java.util.ArrayList;
import java.util.List;

import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.OneToMany;
import javax.persistence.OneToOne;
import javax.persistence.Table;

@Entity
@Table(name = "instructor")
public class Instructor {
		// annotate the class as an entity and map to db table
		// define the fields 
		// annotate the fields with db coloumn names 
		// create constructors 
		// generate gettes/setter method
		// generate toString() method
	
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	@Column(name="id")
	private int id; 
	

	@Column(name="first_name")
	private String firstName; 
	
	@Column(name="last_name")
	private String lastName; 
	
	@Column(name="email")
	private String email; 
	
	@OneToOne(cascade=CascadeType.ALL)
	@JoinColumn(name="instructor_detail_id")
	private InstructorDetail instructorDetailId; 
	
	
	// MappedBy is the name of the variable in Course class which joins this column
	@OneToMany(mappedBy="instructor",
			   cascade={CascadeType.PERSIST,CascadeType.MERGE,
					   CascadeType.DETACH,CascadeType.REFRESH})
	
	
	private List<Course> courses; 
	public Instructor() {
		
	}
	

	public Instructor(String firstName, String lastName, String email) {
		this.firstName = firstName;
		this.lastName = lastName;
		this.email = email;
	}

	
	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getFirstName() {
		return firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	
	public InstructorDetail getInstructorDetailId() {
		return instructorDetailId;
	}


	public void setInstructorDetailId(InstructorDetail instructorDetailId) {
		this.instructorDetailId = instructorDetailId;
	}
	
	

	public List<Course> getCourses() {
		return courses;
	}


	public void setCourses(List<Course> courses) {
		this.courses = courses;
	}


	@Override
	public String toString() {
		return "Instructor [id=" + id + ", firstName=" + firstName + ", lastName=" + lastName + ", email=" + email
				+ ", instructorDetailId=" + instructorDetailId + "]";
	}
	
	// add convenience methods for bi-directional relationship
	public void add(Course tempCourse) {
		if(courses==null) {
			courses= new ArrayList<>();
		}
		courses.add(tempCourse);
		tempCourse.setInstructor(this);
	}
	
}

```
4. Create main App
  - Main App would be created in three varieties 
`/src/com.luv2code.hibernate.demo/CreateInstructorDemo.java`
```Java
package com.luv2code.hibernate.demo;



import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

import com.luv2code.hibernate.demo.entity.Course;
import com.luv2code.hibernate.demo.entity.Instructor;
import com.luv2code.hibernate.demo.entity.InstructorDetail;



public class CreateInstructorDemo {

	public static void main(String[] args) {
		// TODO Create session method 
		SessionFactory factory = new Configuration()
								.configure("hibernate.cfg.xml")
								.addAnnotatedClass(Instructor.class)
								.addAnnotatedClass(Course.class)
								.addAnnotatedClass(InstructorDetail.class)
								.buildSessionFactory(); 
		
		// TODO Create session 
		Session session = factory.getCurrentSession();
		
		try {
			
			// create a object
			
			Instructor tempInstructor = new Instructor("Susan","Public","susan.public@luv2code.com");
			
			InstructorDetail tempInstructorDetail = 
					new InstructorDetail("http://www.youtube.com",
							"Video Games");
			
			// associate a object 
			tempInstructor.setInstructorDetailId(tempInstructorDetail);
			
			
			// start a transaction 
			session.beginTransaction(); 
			
			// save the instructor 
			// Note: This will also save the InstructorDetail object 
			// because of the CascadeType.ALL
			System.out.println("Saving Instructor: "+tempInstructor);
			session.save(tempInstructor);
			
			
			// commit transaction
			session.getTransaction().commit();
			System.out.println("Done");
			
		}
		finally {
			// add clean up code 
			session.close();
			factory.close();
		}

	}


}

```
`/src/com.luv2code.hibernate.demo/CreateCoursesDemo.java`
```Java
package com.luv2code.hibernate.demo;



import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

import com.luv2code.hibernate.demo.entity.Course;
import com.luv2code.hibernate.demo.entity.Instructor;
import com.luv2code.hibernate.demo.entity.InstructorDetail;



public class CreateCoursesDemo {

	public static void main(String[] args) {
		// TODO Create session method 
		SessionFactory factory = new Configuration()
								.configure("hibernate.cfg.xml")
								.addAnnotatedClass(Instructor.class)
								.addAnnotatedClass(Course.class)
								.addAnnotatedClass(InstructorDetail.class)
								.buildSessionFactory(); 
		
		// TODO Create session 
		Session session = factory.getCurrentSession();
		
		try {
			
			// start a transaction 
			session.beginTransaction(); 
			
			// get the instructor from db 
			int theId = 1; 
			Instructor tempInstructor = session.get(Instructor.class, theId);
			
			// create some courses 
			Course tempCourse1 = new Course("Air Guitar - The Ultimate Guide");
			Course tempCourse2 = new Course("The Pinball Masterclass");
			
			
			// add courses to instructor 
			tempInstructor.add(tempCourse1);	
			tempInstructor.add(tempCourse2);
			
			// save the courses
			session.save(tempCourse1);
			session.save(tempCourse2);
			
			// commit transaction
			session.getTransaction().commit();
			System.out.println("Done");
			
		}
		finally {
			// add clean up code 
			session.close();
			
			
			factory.close();
		}

	}

}
```
`/src/com.luv2code.hibernate.demo/CreateInstructorCoursesDemo.java`
```Java
package com.luv2code.hibernate.demo;



import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

import com.luv2code.hibernate.demo.entity.Course;
import com.luv2code.hibernate.demo.entity.Instructor;
import com.luv2code.hibernate.demo.entity.InstructorDetail;



public class GetInstructorCoursesDemo {

	public static void main(String[] args) {
		
		// TODO Create session method 
		SessionFactory factory = new Configuration()
								.configure("hibernate.cfg.xml")
								.addAnnotatedClass(Instructor.class)
								.addAnnotatedClass(Course.class)
								.addAnnotatedClass(InstructorDetail.class)
								.buildSessionFactory(); 
		
		// TODO Create session 
		Session session = factory.getCurrentSession();
		
		try {
			
			// start a transaction 
			session.beginTransaction(); 
			
			// get the instructor from db 
			int theId = 1; 
			Instructor tempInstructor = session.get(Instructor.class, theId);
			System.out.println("Instructor: " + tempInstructor);
			
			// get courses for the instructor 
			System.out.println("Courses: " + tempInstructor.getCourses());
			
			
			// commit transaction
			session.getTransaction().commit();
			System.out.println("Done");
			
		}
		finally {
			// add clean up code 
			session.close();
			
			
			factory.close();
		}

	}


}

```
`/src/com.luv2code.hibernate.demo/DeleteCoursesDemo.java`
```Java
package com.luv2code.hibernate.demo;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

import com.luv2code.hibernate.demo.entity.Course;
import com.luv2code.hibernate.demo.entity.Instructor;
import com.luv2code.hibernate.demo.entity.InstructorDetail;



public class DeleteCoursesDemo {

	public static void main(String[] args) {
		
		// TODO Create session method 
		SessionFactory factory = new Configuration()
								.configure("hibernate.cfg.xml")
								.addAnnotatedClass(Instructor.class)
								.addAnnotatedClass(Course.class)
								.addAnnotatedClass(InstructorDetail.class)
								.buildSessionFactory(); 
		
		// TODO Create session 
		Session session = factory.getCurrentSession();
		
		try {
			
			// start a transaction 
			session.beginTransaction(); 
			
			// get a course 
			int theId = 10; 
			Course tempCourse = session.get(Course.class, theId);
			
			// delete a course 
			System.out.println("Deleting Course: " + tempCourse);
			session.delete(tempCourse);
			
				
			// commit transaction
			session.getTransaction().commit();
			System.out.println("Done");
			
		}
		finally {
			// add clean up code 
			session.close();
			factory.close();
		}

	}


}

```