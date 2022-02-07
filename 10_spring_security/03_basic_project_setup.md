## Implementation

**Directory Structure**
```
src
├───java
│   └───com
│       └───luv2code
│           └───springsecurity
│               └───demo
│                   ├───config
│                   │       DemoAppConfig.java
│                   │       MySpringMvcDispatcherServletIntializer.java
│                   │
│                   └───controller
│                           DemoController.java
│
└───WEB-INF
        └───view
                home.jsp
```
**DemoAppConfig.java**
```Java
package com.luv2code.springsecurity.demo.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.view.InternalResourceViewResolver;

@Configuration
@EnableWebMvc
@ComponentScan(basePackages="com.luv2code.springsecurity")
public class DemoAppConfig {

	
	// define a bean for ViewResolver
	@Bean
	public ViewResolver viewResolver() {
		InternalResourceViewResolver viewResolver = new InternalResourceViewResolver(); 
		viewResolver.setPrefix("/WEB-INF/view/");
		viewResolver.setSuffix(".jsp");
		return viewResolver;
	}
	
}
```

**MySpringMvcDispatcherServletIntializer.java**
```Java
package com.luv2code.springsecurity.demo.config;

import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

public class MySpringMvcDispatcherServletIntializer extends AbstractAnnotationConfigDispatcherServletInitializer {

	@Override
	protected Class<?>[] getRootConfigClasses() {
		// No root config class for our app
		return null;
	}

	@Override
	protected Class<?>[] getServletConfigClasses() {
		 return new Class[] { DemoAppConfig.class };
	}

	@Override
	protected String[] getServletMappings() {
		return new String[] { "/" };
	}

}
```
**DemoController.java**
```Java
package com.luv2code.springsecurity.demo.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class DemoController {
	
	@GetMapping("/")
	public String showHome() {
		
		
		return "home";
	}
}
```
**home.jsp**
```html
<html>
	<head>
		<title>luv2code Company Home Page</title>
	</head>
    <body>
    	<h2>luv2code Company Home Page</h2>
    	<hr>
        Welcome to the luv2code company home page!
    </body>
</html>
```


