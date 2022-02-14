## Database Access 
- So far, our user accounts were hard coded in Java source code
- We want to add database access

## Database Support in Spring Security
- Spring Security can read user account info from database
- By default, you have to follow Spring Security’s predefined table schemas

## Customize Database Access with Spring Security
- Can also customize the table schemas
- Useful if you have custom tables specific to your project / custom
- You will be responsible for developing the code to access the data
    - JDBC, Hibernate etc…  

## Development Process
1. Develop SQL Script to set up database tables
2. Add database support to Maven POM file
3. Create JDBC properties file
4. Define DataSource in Spring Configuration
5. Update Spring Security Configuration to use JDBC Step-By-Step

## Step 1: Develop SQL Script to setup database table
```sql
CREATE TABLE `users` (
    `username` varchar(50) NOT NULL,
    `password` varchar(50) NOT NULL,
    `enabled` tinyint(1) NOT NULL,
    PRIMARY KEY (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

/*
 *{noop} The encoding algorithm id 
 * Let the spring security know passwords are stored in plain text (noop)
 */
INSERT INTO `users` VALUES
('john','{noop}test123',1),
('mary','{noop}test123',1),
('susan','{noop}test123',1);

/*
 * Authorities table (same as roles )
 */
CREATE TABLE `authorities` (
    `username` varchar(50) NOT NULL,
    `authority` varchar(50) NOT NULL,
    UNIQUE KEY `authorities_idx_1` (`username`,`authority`),
    CONSTRAINT `authorities_ibfk_1`
    FOREIGN KEY (`username`)
    REFERENCES `users` (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;


INSERT INTO `authorities` VALUES 
('john','ROLE_EMPLOYEE'),
('mary','ROLE_EMPLOYEE'),
('mary','ROLE_MANAGER'),
('susan','ROLE_EMPLOYEE'),
('susan','ROLE_ADMIN');
```

## Spring Security Password Storage
- In Spring Security 5, passwords are stored using a specific format
- `{id}encodedPassword`

|ID|Description|
|--|-----------|
|noop| Plain text passwords|
|bcrypt| BCrypt password hashing|
|..|..|

## Step 2: Add Database Support to Maven POM file
```xml
<!-- MySQL -->
<!-- JDBC Driver -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.45</version>
</dependency>
```
```xml
<!-- C3PO -->
<!-- DB Connection pool -->
<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.5.2</version>
</dependency>
```

## Step 3: Create JDBC Properties File
**src/main/resources/persistence-mysql.properties**
```
#
# JDBC connection properties
#
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/spring_security_demo?useSSL=false
jdbc.user=springstudent
jdbc.password=springstudent
#
# Connection pool properties
#
connection.pool.initialPoolSize=5
connection.pool.minPoolSize=5
connection.pool.maxPoolSize=20
connection.pool.maxIdleTime=3000
```

## Step 4: Define DataSource in Spring Configuration
```Java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.luv2code.springsecurity.demo")
/*
 * Will read the props file src/main/resources
 * files are automatically copied to classpath during Maven build
 */
@PropertySource("classpath:persistence-mysql.properties")
public class DemoAppConfig {
    
    @Autowired
    private Environment env;
    private Logger logger = Logger.getLogger(getClass().getName());
    
    @Bean
    public DataSource securityDataSource() {
        // create connection pool
        ComboPooledDataSource securityDataSource = new ComboPooledDataSource();
        // set the jdbc driver
    try {
        securityDataSource.setDriverClass(env.getProperty("jdbc.driver"));
    }
    catch(PropertyVetoException exc) {
        throw new RuntimeException(exc);
    }

    // for sanity's sake, let's log url and user ... just to make sure we are reading the data
    logger.info(">>>> jdbc.url=" + env.getProperty("jdbc.url"));
    logger.info(">>>> jdbc.user=" + env.getProperty("jdbc.user"));
    
    // set database connection props
    securityDataSource.setJdbcUrl(env.getProperty("jdbc.url"));
    securityDataSource.setUser(env.getProperty("jdbc.user"));
    securityDataSource.setPassword(env.getProperty("jdbc.password"));
    
    // set connection pool props
    securityDataSource.setInitialPoolSize(Integer.parseInt(env.getProperty("connection.pool.initialPoolSize")));
    securityDataSource.setMinPoolSize(Integer.parseInt(env.getProperty("connection.pool.minPoolSize")));
    securityDataSource.setMaxPoolSize(Integer.parseInt(env.getProperty("connection.pool.maxPoolSize")));
    securityDataSource.setMaxIdleTime(Integer.parseInt(env.getProperty("connection.pool.maxIdleTime")));
    return securityDataSource;

}
```

## Step 5: Update Spring Security to use JDBC

```Java
@Configuration
@EnableWebSecurity
public class DemoSecurityConfig extends WebSecurityConfigurerAdapter {
    
    /*
     * Inject our data source that we just configured
     */
    @Autowired
    private DataSource securityDataSource;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        /*
         * No longer hard-coding users :-)
         * Tell Spring Security to use JDBC authentication with our data source
         */
        auth.jdbcAuthentication().dataSource(securityDataSource);
    }
…
}
```


## SQL Scripts 
```sql
DROP DATABASE  IF EXISTS `spring_security_demo_plaintext`;

CREATE DATABASE  IF NOT EXISTS `spring_security_demo_plaintext`;
USE `spring_security_demo_plaintext`;

--
-- Table structure for table `users`
--

DROP TABLE IF EXISTS `users`;
CREATE TABLE `users` (
  `username` varchar(50) NOT NULL,
  `password` varchar(50) NOT NULL,
  `enabled` tinyint(1) NOT NULL,
  PRIMARY KEY (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

--
-- Inserting data for table `users`
--

INSERT INTO `users` 
VALUES 
('john','{noop}test123',1),
('mary','{noop}test123',1),
('susan','{noop}test123',1);


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
-- Inserting data for table `authorities`
--

INSERT INTO `authorities` 
VALUES 
('john','ROLE_EMPLOYEE'),
('mary','ROLE_EMPLOYEE'),
('mary','ROLE_MANAGER'),
('susan','ROLE_EMPLOYEE'),
('susan','ROLE_ADMIN');

```