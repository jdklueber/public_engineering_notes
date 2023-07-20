# JWT and Spring Boot

[See sample code on github:](https://github.com/jdklueber/jwt_scraps)

## Java side:
1. Include in pom file:
```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
            <version>0.9.1</version>
        </dependency>
        <dependency>
            <groupId>jakarta.xml.bind</groupId>
            <artifactId>jakarta.xml.bind-api</artifactId>
            <version>2.3.2</version>
        </dependency>
```
    This is a potential source of errors for students:  a LOT of wonky stack traces can come out if the jakarta.xml.bind dep isn't included!!
2. Copy JWTUtil into project
3. Implement UserDetailsService
4. Implement JWTAuthenticationFilter
5. Implement SecurityConfig
        
        I think we also have to use the PasswordEncoder bean when we save our password to the database...
        
6. Implement the login controller/services

### configuration.JWTAuthenticationFilter
This bit basically grabs the JWT off the request, validates it against the user, and generates the Spring Security context stuff that lets the rest of Spring Security work.

### configuration.SecurityConfig
This is where the Spring Security configuration lives.  It's also where the jwtAuthenticationFilter gets plugged into the system.  Note that the sample code is using NoOpPasswordEncoder.  Probably use something more sensible here.

### configuration.WebConfig
I can't remember right now what problem this solved, but apparently we needed to just say YES IT'S FINE FOR CORS TO HAPPEN EVERYWHERE HERE.  Oh yeah, I think it was to allow the react dev server to serve up the front end for this.

### controller.LoginController
Here's where the username and password get authenticated and a JWT returned.  Also where the refresh token gets generated.

### security.JWTUtil
Boilerplate code to create and validate JWTs.  The "secret" string at the top should probably be pulled out of application.properties file or similar.

### security.UserDetailsServiceImpl
Implements UserDetailsService.  As is, just uses "foo" and "foo" for username and password, but should be hooked into a user database.

## React side
The salient part is that when you call protected endpoints, you need to do this:
```javascript
        headers.append('Content-Type', 'application/json');
        headers.append('Authorization', 'Bearer ' + jwt);
```
in order to get them to successfully use the JWT.

# Troubleshooting
1. this can blow up tests, if they're annotated like so:
```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.NONE)
class MyServiceTest {
    // snip!
}
```
If that's causing problems, dump the webEnvironment argument.