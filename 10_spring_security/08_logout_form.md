## Development Process
1. Add logout support to Spring Security Configuration
2. Add logout button to JSP page
3. Update login form to display “logged out” message

```

[LOGOUT]-------------------Clear User------------> [You have been logget out] 
                             Session
```
## Step 1: Add Logout support to Spring Security Configuration
```Java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()
    .anyRequest().authenticated()
    .and()
    .formLogin()
    .loginPage("/showMyLoginPage")
    .loginProcessingUrl("/authenticateTheUser")
    .permitAll()
    .and()
    .logout().permitAll(); /// add logout support for default URL /logout

}
```

## Step 2: Add logout button
- Send data to default logout URL: /logout
- Logout URL will be handled by Spring Security Filters
- You get it for free … no coding required
- Send data to default logout URL: /logout
- By default, must use POST method
```html
<form:form action="${pageContext.request.contextPath}/logout" method="POST">
    <input type="submit" value="Logout" />
</form:form>
```

## Logout process
- When a logout is processed, by default Spring Security will …
- Invalidate user’s HTTP session and remove session cookies, etc
- Send user back to your login page
- Append a logout parameter: ?logout

## Step 3: Update login form to display “logged out” message
1. Update login form
    1.Check the logout parameter
    2.If logout parameter exists, show “logged out” message
```html
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
…
<form:form action="…" method="…">
    <c:if test=“${param.logout != null}”>
        u have been logged out.</i>
    </c:if>
    User name: <input type="text" name="username" />
    Password: <input type="password" name="password" />
</form:form>
```
`http://localhost:8080/myapp/showMyLoginPage?logout` /// if logout param then show the message
