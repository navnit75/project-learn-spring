## Spring Security Password Encryption
- So far, our user passwords are stored in plaintext.
- The best practice is store passwords in an encrypted format
- Spring Security recommends using the popular bcrypt algorithm

## bcrypt
- Performs one-way encrypted hashing
- Adds a random salt to the password for additional protection
- Includes support to defeat brute force attacks

## Bcrypt Additional Information
- Why you should use bcrypt to hash passwords [bycrypt](www.luv2code.com/why-bcrypt)
    - Detailed bcrypt algorithm analysis [algo-analysis](www.luv2code.com/bcrypt-wiki-page)
    - Password hashing - Best Practices [best-practices](www.luv2code.com/password-hashing-best-practices)

## How to get Bcrypt password
- You have a plaintext password and you want to encrypt using bcrypt
    - Option 1: Use a website utility to perform the encryption
    - Option 2: Write Java code to perform the encryption

## How to Get a Bcrypt password - Website
- Visit: www.luv2code.com/generate-bcrypt-password
- Enter your plaintext password
- The website will generate a bcrypt password for you

## Development Process
1. Run SQL Script that contains encrypted passwords
    A. Modify DDL for password field, length should be 68
2. Modify database properties file to point to new database schema

## Password Storage
- Password column must be at least 68 chars wide
```
{bcrypt} - 8 chars
encodedPassword - 60 chars
```

## Modify DDL for Password Field
```sql
CREATE TABLE `users` (
    `username` varchar(50) NOT NULL,
    `password` char(68) NOT NULL,
    `enabled` tinyint(1) NOT NULL,
    PRIMARY KEY (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

/*
 *{bcrypt} : The encoding algorithm id 
 * {$2a$04$eFytJDGtjbThXa80FyOOBuFdK2IwjyWefYkMpiBEFlpBwDH.5PM0K}fun123
 */
INSERT INTO `users` VALUES
('john','{bcrypt}$2a$04$eFytJDGtjbThXa80FyOOBuFdK2IwjyWefYkMpiBEFlpBwDH.5PM0K',1),
('mary','{bcrypt}$2a$04$eFytJDGtjbThXa80FyOOBuFdK2IwjyWefYkMpiBEFlpBwDH.5PM0K',1),
('susan','{bcrypt}$2a$04$eFytJDGtjbThXa80FyOOBuFdK2IwjyWefYkMpiBEFlpBwDH.5PM0K',1);
```

## SQL Scripts 
```sql
DROP DATABASE  IF EXISTS `spring_security_demo_bcrypt`;

CREATE DATABASE  IF NOT EXISTS `spring_security_demo_bcrypt`;
USE `spring_security_demo_bcrypt`;

--
-- Table structure for table `users`
--

DROP TABLE IF EXISTS `users`;
CREATE TABLE `users` (
  `username` varchar(50) NOT NULL,
  `password` char(68) NOT NULL,
  `enabled` tinyint(1) NOT NULL,
  PRIMARY KEY (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

--
-- Dumping data for table `users`
--
-- NOTE: The passwords are encrypted using BCrypt
--
-- A generation tool is avail at: http://www.luv2code.com/generate-bcrypt-password
--
-- Default passwords here are: fun123
--

INSERT INTO `users` 
VALUES 
('john','{bcrypt}$2a$04$eFytJDGtjbThXa80FyOOBuFdK2IwjyWefYkMpiBEFlpBwDH.5PM0K',1),
('mary','{bcrypt}$2a$04$eFytJDGtjbThXa80FyOOBuFdK2IwjyWefYkMpiBEFlpBwDH.5PM0K',1),
('susan','{bcrypt}$2a$04$eFytJDGtjbThXa80FyOOBuFdK2IwjyWefYkMpiBEFlpBwDH.5PM0K',1);


--
-- Table structure for table `authorities`
--

DROP TABLE IF EXISTS `authorities`;
CREATE TABLE `authorities` (
  `username` varchar(50) NOT NULL,
  `authority` varchar(50) NOT NULL,
  UNIQUE KEY `authorities_idx_1` (`username`,`authority`),
  CONSTRAINT `authorities_ibfk_1` FOREIGN KEY (`username`) REFERENCES `users` (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

--
-- Dumping data for table `authorities`
--

INSERT INTO `authorities` 
VALUES 
('john','ROLE_EMPLOYEE'),
('mary','ROLE_EMPLOYEE'),
('mary','ROLE_MANAGER'),
('susan','ROLE_EMPLOYEE'),
('susan','ROLE_ADMIN');
```

- At last changing the content of the properties file. 

