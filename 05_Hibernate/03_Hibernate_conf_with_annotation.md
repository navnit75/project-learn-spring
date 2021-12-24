## Steps 
- Add hibernate configuration file 
- Annotate java class 
- Annotate the java code to perform databas operation

## This is the hibernate configuration file 
`src/hibernate.cfg.xml`
```xml
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

<hibernate-configuration>

    <session-factory>

        <!-- JDBC Database connection settings -->
        <property name="connection.driver_class">com.mysql.cj.jdbc.Driver</property>
        <property name="connection.url">jdbc:mysql://localhost:3306/hb_student_tracker?useSSL=false&amp;serverTimezone=UTC</property>
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
## Terminology 
- `Entity Class : `Java Class that is mapped to the database table

## Two options with mapping
- Option 1: XML config file (legacy)
- Option 2: Java annotation (Modern, Preferred)

## Java Annotation 
- Map Class to database table
- Map fields to database columns

## Can be an interview question
- FAQ: Why we are using JPA Annotation instead of Hibernate ?
1. QUESTION:
Why we are using JPA Annotation instead of Hibernate ?
For example, why we are not using this org.hibernate.annotations.Entity?

ANSWER:
```
JPA is a standard specification. Hibernate is an implementation of the JPA specification.
Hibernate implements all of the JPA annotations.
The Hibernate team recommends the use of JPA annotations as a best practice.
```

## Various Terminologies

| Class | Description| 
|-------|------------|
|Session Factory | Reads the hibernate config file |
||Create session objects | 
||Heavy Weight objects |
||Once create in your app |
| Session | Wraps a JDBC connection | 
|| Main object used to save/retreive objects |
|| Short lived objects |
|| Retrieved from session factory|
