# Controller Advice / Exception Handling

## Process
Basically, make a ControllerAdvice annotated controller which has an ExceptionHandler annotated method to return the error message you want returned.

Below is a generic handler that'll return a 500 no matter what.  

This may or may not be what you actually want.

## Sample Code

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

@ControllerAdvice
public class GenericControllerAdvice {
    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleException(Exception ex) { 
        return new ResponseEntity<String>(
                "Something went wrong on our end. Your request failed. :(",
                HttpStatus.INTERNAL_SERVER_ERROR);
    }
}

````