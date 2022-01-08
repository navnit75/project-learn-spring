## Fetch Types : Eager Vs Lazy
- When we fetch / retrieve data, should we retrieve EVERYTHING? 
- Eager will retrieve everything. 
- Lazy will retrieve on request. 

## Eager Loading: 
- Eager loading will load all dependent entities. 
- Load instructor and all of their courses at once. 
- Now when courses are one or two its easier. 
- Imagine the scenerio when #courses are more. 
- May lead to low performance. [Could easily turn into performance nightmare. ]

### Use Case
- In our main app, if we are searching for a course by keyword. 
- Only want a list of matching courses. 
- Eager loading would still `load all students for each course`...not good. 
- `BEST PRACTICE`, only load data when absolutely needed 
- Prefer `LAZY LOADING` instead of `EAGER LOADING`. 

## LAZY LOADING 
- Lazy loading will load the main entity first. 
- Load dependent entities on demand(lazy).
- Load Course First ----> Load(students) on demand(lazy). 

## Real World Use Case
- Scenerio is of Website with Details of Instructors 
- A search button to search the instructor 
- Right side column holds the link to full detail view of the Instructor 
- In master view , use lazy loading (only load instructors , not the courses)
- In detail view , retrieve the entity and necessary dependent entities. (Load instructor and their courses)

## Where Fetch Type Comes in Code
- When you define the mapping relationship 
- You can specify the fetch type: EAGER or LAZY
```Java
@Entity
@Table(name="instructor")
public class Instructor{
    ...
    @OneToMany(fetch=FetchType.LAZY,mappedBy="instructor")
    private List<Course> courses;
    ...
}
```

## Default Fetcy Types
|Mapping|Default Fetch Types|
|-------|-------------------|
|@OneToOne|FetchType.EAGER|
|@OneToMany|FetchType.LAZY|
|@ManyToOne|FetchType.EAGER|
|@ManyToMany|FetchType.LAZY|

## Overriding default fetch type
```Java
@ManyToOne(fetch=FetchType.LAZY)
@JoinColumn(name="instructor_id")
private Instructor instructor; 
```
## More about lazy loading
- The data is retrieved only on demand
- However , this requires an open Hibernate Session. 
- Need an connection to database to retrieve data. 
- If its closed, it would throw an exception. 
- Two Options: 
    - Option 1: Session.get and call appropriate getter method(s)
    - Option 2: Hibernate query with HQL 
- Note: Other techniques are available but the two above are most common. 

## For example checkout project: hb-eager-vs-lazy-demo

## Breaking 
```Java
package com.luv2code.hibernate.demo;



import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

import com.luv2code.hibernate.demo.entity.Course;
import com.luv2code.hibernate.demo.entity.Instructor;
import com.luv2code.hibernate.demo.entity.InstructorDetail;



public class EagerlazyDemo {

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
			System.out.println("luv2code: Instructor: " + tempInstructor);
			
			// commit transaction
			session.getTransaction().commit();
			
			// close the session
			session.close();
			
			// since courses are lazy loaded ... this should fail
			
			// get courses for the instructor 
			System.out.println("luv2code: Courses: " + tempInstructor.getCourses());
			
			
			
			System.out.println("luv2code: Done");
			
		}
		finally {
			// add clean up code 
			session.close();
			
			
			factory.close();
		}

	}


}

```

## Resolving issue using option 1 
```Java
package com.luv2code.hibernate.demo;



import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

import com.luv2code.hibernate.demo.entity.Course;
import com.luv2code.hibernate.demo.entity.Instructor;
import com.luv2code.hibernate.demo.entity.InstructorDetail;



public class EagerlazyDemo {

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
			System.out.println("luv2code: Instructor: " + tempInstructor);
			
			System.out.println("luv2code: Courses: " + tempInstructor.getCourses());
			
			// commit transaction
			session.getTransaction().commit();
			
			// close the session
			session.close();
			
			System.out.println("luv2code: The session is now closed!\n");
			
			// get courses for the instructor 
			System.out.println("luv2code: Courses: " + tempInstructor.getCourses());
			
			
			
			System.out.println("luv2code: Done");
			
		}
		finally {
			// add clean up code 
			session.close();
			
			
			factory.close();
		}

	}


}

```

## Resolving the Issue using option 2: 
```Java
package com.luv2code.hibernate.demo;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;
import org.hibernate.query.Query;

import com.luv2code.hibernate.demo.entity.Course;
import com.luv2code.hibernate.demo.entity.Instructor;
import com.luv2code.hibernate.demo.entity.InstructorDetail;



public class EagerlazyDemo {

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
			
			Query<Instructor> query = 
					session.createQuery("select i from Instructor i "
														 + "JOIN FETCH i.courses "
														 + "where i.id=:theInstructorId",
															Instructor.class);
			
			// set parameter on query
			query.setParameter("theInstructorId",theId);
			
			//execute query and get instructor
			Instructor tempInstructor = query.getSingleResult();
			
			System.out.println("luv2code: Instructor: " + tempInstructor);
			
			System.out.println("luv2code: Courses: " + tempInstructor.getCourses());
			
			// commit transaction
			session.getTransaction().commit();
			
			// close the session
			session.close();
			
			System.out.println("luv2code: The session is now closed!\n");
			
			// get courses for the instructor 
			System.out.println("luv2code: Courses: " + tempInstructor.getCourses());
			
			
			
			System.out.println("luv2code: Done");
			
		}
		finally {
			// add clean up code 
			session.close();
			
			
			factory.close();
		}

	}


}

```

## Opening session later 
```Java
package com.luv2code.hibernate.demo;
import java.util.List;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;
import org.hibernate.query.Query;
import com.luv2code.hibernate.demo.entity.Course;
import com.luv2code.hibernate.demo.entity.Instructor;
import com.luv2code.hibernate.demo.entity.InstructorDetail;
public class GetCoursesLater {
    public static void main(String[] args) {
        // create session factory
        SessionFactory factory = new Configuration()
                                .configure("hibernate.cfg.xml")
                                .addAnnotatedClass(Instructor.class)
                                .addAnnotatedClass(InstructorDetail.class)
                                .addAnnotatedClass(Course.class)
                                .buildSessionFactory();
        
        // create session
        Session session = factory.getCurrentSession();
        
        try {            
            
            // start a transaction
            session.beginTransaction();
                        
            // get the instructor from db
            int theId = 1;
            Instructor tempInstructor = session.get(Instructor.class, theId);                    
            
            System.out.println("luv2code: Instructor: " + tempInstructor);    
            
            // commit transaction
            session.getTransaction().commit();
            
            // close the session
            session.close();
            System.out.println("\nluv2code: The session is now closed!\n");
            //
            // THIS HAPPENS SOMEWHERE ELSE / LATER IN THE PROGRAM
            // YOU NEED TO GET A NEW SESSION
            //
            
            System.out.println("\n\nluv2code: Opening a NEW session \n");
            session = factory.getCurrentSession();
            
            session.beginTransaction();
            
            // get courses for a given instructor
            Query<Course> query = session.createQuery("select c from Course c "
                                                    + "where c.instructor.id=:theInstructorId",    
                                                    Course.class);
            
            query.setParameter("theInstructorId", theId);
            
            List<Course> tempCourses = query.getResultList();
            
            System.out.println("tempCourses: " + tempCourses);
            
            // now assign to instructor object in memory
            tempInstructor.setCourses(tempCourses);
            
            System.out.println("luv2code: Courses: " + tempInstructor.getCourses());
            
            session.getTransaction().commit();
            
            System.out.println("luv2code: Done!");
        }
        finally {
            
            // add clean up code
            session.close();
            
            factory.close();
        }
    }
}
```