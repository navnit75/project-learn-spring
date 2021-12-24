## Steps 
- Adding temp user into the MySQL 
```sql
CREATE USER 'hbstudent'@'localhost' IDENTIFIED BY 'hbstudent';

GRANT ALL PRIVILEGES ON * . * TO 'hbstudent'@'localhost';

#
# Starting with MySQL 8.0.4, the MySQL team changed the 
# default authentication plugin for MySQL server 
# from mysql_native_password to caching_sha2_password.
#
# The command below will make the appropriate updates for your user account.
#
# See the MySQL Reference Manual for details: 
# https://dev.mysql.com/doc/refman/8.0/en/caching-sha2-pluggable-authentication.html
#
ALTER USER 'hbstudent'@'localhost' IDENTIFIED WITH mysql_native_password BY 'hbstudent';
```

- Adding a table into the MySQL 

```sql
--
-- Creation of database
--
CREATE DATABASE  IF NOT EXISTS `hb_student_tracker`;
USE `hb_student_tracker`;

--
-- Table structure for table `student`
--

DROP TABLE IF EXISTS `student`;

CREATE TABLE `student` (
  -- Here AUTO INCREMENT is used to keep incrementing and adding row
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `first_name` varchar(45) DEFAULT NULL,
  `last_name` varchar(45) DEFAULT NULL,
  `email` varchar(45) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=latin1;
```
- Later add new connection , with username = `hbstudent` and pass = `hbstudent`
- Download Hibernate files from [https://www.hibernate.org](www.hibernate.org). 
- Download Connector files from [https://dev.mysql.com](dev.mysql.com).
- Add the required lib files and , connector lib files to separate folder of lets says `project/lib`. 
- Add these libraries to class path by changing build path. 
- Create some package. For ex. `com.luv2code.jdbc`
- Create a class. For ex. `com.luv2code.jdbc/TestJdbc.java`
- Connection tester code for Java. 
`TestJdbc.java`
```
package com.luv2code.jdbc;

import java.sql.Connection;
import java.sql.DriverManager;

public class TestJdbc {
	public static void main(String args[]) {
		String jdbcUrl = "jdbc:mysql://localhost:3306/hb_student_tracker?useSSL=false";
		String user = "hbstudent";
		String pass = "hbstudent";
		try {
			
			System.out.println("Connecting to the database: "+jdbcUrl);
			Connection myCon = 
						DriverManager.getConnection(jdbcUrl,user,pass);
			System.out.println("Connection successful!!!");
		}
		catch(Exception e) {
			e.printStackTrace();
		}
	}
}

```