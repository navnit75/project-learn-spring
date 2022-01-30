## High level view of Working
```
Web Browser ----> Customer Controller -----> Customer DAO ( Data access object) -----> Database
 ^                     |
 |                     V
 +-----------------Jsp Pages
```

## Customer Data Access Object 
- Responsible for interfacing with the database
- This is a common design pattern: Data Access Object(DAO) [Really just a helper class /utility class ]
- In our project we would be taking use of Hibernate APIs. 
- For Ex: Customer Data Access Object. 

|Methods|
|--------|
|saveCustomer(...)|
|getCustomer(...)|
|getCustomers(...)|
|updateCustomer(...)|
|deleteCustomer(...)|

## List Customer 
1. Create Customer.java
2. Create CustomerDAO.java
    - and CustomerDAOImpl.java
3. Create CustomerController.java
4. Create JSP page: list-customers.jsp

## Entity Class (Refresher)
- Java class that is mapped to a database table

## How to tell Spring MVC where to look for 
- Remember in spring mvc config file 
```xml
	<bean id="sessionFactory"
		class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
		<property name="dataSource" ref="myDataSource" />

        <!-- You can see here -->
        <!-- To add multiple packages, we can use comma to add multiple packages -->
		<property name="packagesToScan" value="com.luv2code.springdemo.entity" />
		<property name="hibernateProperties">
		   <props>
		      <prop key="hibernate.dialect">org.hibernate.dialect.MySQLDialect</prop>
		      <prop key="hibernate.show_sql">true</prop>
		   </props>
		</property>
   </bean>
```

## Customer Data Access Object 
- For hibernate, our DAO needs a Hibernate SessionFactory
- Our hibernate Session factory needs a Data Source. 
- The datasource defines database connection info. 
- These are all dependencies !
- We will wire them together with Dependency Injection (DI)

```
Customer DAO <------> Session Factory <--------->Data Source <-------> Database 
```

`Datasource` 
```xml
<bean id="myDataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource"
          destroy-method="close">
          <!-- These few steps define how to connect to database -->
        <property name="driverClass" value="com.mysql.cj.jdbc.Driver" />
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/web_customer_tracker?useSSL=false&amp;serverTimezone=UTC" />
        <property name="user" value="springstudent" />
        <property name="password" value="springstudent" /> 

        <!-- these are connection pool properties for C3P0 -->
        <property name="minPoolSize" value="5" />
        <property name="maxPoolSize" value="20" />
        <property name="maxIdleTime" value="30000" />
	</bean>  
```

`SessionFactory`
- Session Factory depends on data source so you can see reference to data source in the declaration. 
```xml
<bean id="sessionFactory"
		class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
		<property name="dataSource" ref="myDataSource" />
		<property name="packagesToScan" value="com.luv2code.springdemo.entity" />
		<property name="hibernateProperties">
		   <props>
		      <prop key="hibernate.dialect">org.hibernate.dialect.MySQLDialect</prop>
		      <prop key="hibernate.show_sql">true</prop>
		   </props>
		</property>
</bean>	  
```

`CustomerDAO `
- Define DAO interface . 
- Define DAO implementation. 
	- Inject the session factory. 

## Spring `@Transactional`
- Spring provides an **@Transactional** annotation. 
- **Automagically** begin and end a transaction for your Hibernate code. 
	- No need for you to explicitly do this in your code. 
- The Spring magic happens behind the scenes. 
- So earlier 

```Java
// start a transaction
session.beginTransaction();

// DO YOUR HIBERNATE STUFF HERE 
// ...

// commit transaction
session.getTransaction().commit();
```



## Specialized Annotation for DAOs
- Spring provides the @Repository annotation
```
                @Component
				/        \
		@Controller    @Repository
```
- @Component : Java Beans 
- @Controller: Spring MVC 
- @Repository: DAO implementation
- Spring will automatically register the DAO implementation, thanks to component scanning. 
- Spring will also provides translation of any JDBC related exception. 


- Do note how to provide different directories for the js/images/

## Welcome File 
- http://localhost:8080/web-customer-tracking
1. Server will look for a welcome file, if it doesn't find one, then you'll get 404
2. Welcome files are configured in `web.xml`


## @GetMapping and @PostMapping
- GET : Request data from given resource 
- POST : Submits data to given resource


## Handling form Submission 
- As we have already seen while creating a CRUD app. 
- 
```Java
@RequestMapping("/processForm")
public String processForm(...){
	...
}
```
- This mapping handles `ALL HTTP` methods 
	- GET, POST  etc

## Constraining the Request Mapping - GET
```Java
@RequestMapping(path="/processForm", method=RequestMethod.GET)
public String processForm(...){
	...
}
```
- This mapping only handles GET method. 
- Any other HTTP request method will get rejected.
- `Annotation based shortcut`
```Java
@GetMapping("/processForm")
public String processForm(...){
	...
}
```

## Constraining the Request Mapping - POST
```Java
@RequestMapping(path="/processForm", method=RequestMethod.POST)
public String processForm(...){
	...
}
```
- only handles POST method. 
- `Annotation based shortcut`
```Java
@PostMapping("/processForm")
public String processForm(...){
	...
}
```

## Difference between GET ... and POST
### GET 
- Good for debugging 
- Bookmark or email URL
- Limitations on data length 

### POST
- Can't bookmark or email URL 
- No limitations on data length 
- Can also send binary data

## Define Services with @Service
- Service Facade design pattern. 
- Intermediate layer for custom bussiness logic
- Integreate data from multiple sources(DAO/repositories)
```
[Customer Controller]<------->[Customer Service]<--------->[Customer DAO]<------->DB
                                                    |
													+----->[Sales DAO]<---------->DB
													|
													+----->[License DAO]<-------->DB
```
## Specialized Annotation for Services 
- Spring provides the `@Services` annotation. 
```
					Component
				   /    |    \
				  /     |     \
				 /      |      \
		@Controller @Repository @Service
```
- `@Service` applied to Service Implementation. 
- Spring will automatically register the Service Implementation. 
	- Thanks to component scanning


## Developement Process
- Define Service interface
- Define service implementation. 
	- Inject the CustomerDAO

## Questions ? 
- Why do we have to create Service layer what has the same functions as DAO layer? Is it necessary to create all this layers?
- Answer
```
And here is a another scenario where you would like to perform transaction management at the service layer.

You can use @Transactional at the service layer if you want DAO methods to run in the same transaction.

Say for example we have

BankDAO

- deposit(...)

- withdraw(...)

If we are transferring funds, we want that to run in the same transaction. By making use of @Transactional at service layer, then we can have this transactional support and both methods will run in the same transaction. This would call deposit() and withdraw(). If either of those methods failed then we'd want to roll the transaction back.

However, if we had @Transactional at DAO level instead of service level, then the methods deposit() and withdraw() would run in separate transactions. If one of them failed, then we would not be able to rollback the other method ... because it is in a separate transaction.

So that's one real-time project use case for applying @Transactional at the Service layer.



Of course, in your personal project, there is no strict requirement to use layers. In fact, there is no requirement to use DAO. You could add all of your code to one controller class. But from an architectural point of view, that would result in a poor design.
```