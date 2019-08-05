# Spring 的参数校验机制

## 1、定义 Domain Class 

```Java
@Entity
public class User {
     
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;
     
    @NotBlank(message = "Name is mandatory")
    private String name;
     
    @NotBlank(message = "Email is mandatory")
    private String email;
     
    // standard constructors / setters / getters / toString
         
}
```

在Bean文件上的字段上加上对应的校验注解(@NotBlank)。

@NotBlank注解表明该字段必须不为空且进行trim操作后长度不为0（除空格外还存在字符）

## 2、实现一个 REST Controller

```Java
@RestController
public class UserController {
 
    @PostMapping("/users")
    ResponseEntity<String> addUser(@Valid @RequestBody User user) {
        // persisting the user
        return ResponseEntity.ok("User is valid");
    }
     
    // standard constructors / other methods
     
}
```

当Spring Boot 发现一个参数加上 @Valid 注解，它会自动启动  JSR 380 的默认实现 —— Hibernate Validator 进行参数校验.

当目标参数校验失败 Spring Boot 会抛出方法参数校验未通过异常（[*MethodArgumentNotValidException*](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/MethodArgumentNotValidException.html)）

## 3、异常处理注解 @ExceptionHandle

@ExceptionHandle 注解允许我们通过一个简单的方法处理特定的异常，我们可以通过它来处理参数校验异常:

```Java
@ResponseStatus(HttpStatus.BAD_REQUEST)
@ExceptionHandler(MethodArgumentNotValidException.class)
public Map<String, String> handleValidationExceptions(
  MethodArgumentNotValidException ex) {
    Map<String, String> errors = new HashMap<>();
    ex.getBindingResult().getAllErrors().forEach((error) -> {
        String fieldName = ((FieldError) error).getField();
        String errorMessage = error.getDefaultMessage();
        errors.put(fieldName, errorMessage);
    });
    return errors;
}
```

该方法将每个未验证通过字段的名称和验证后错误消息存储在一个 Map 中。然后，将这些错误信息以Json格式发送给调用客户端。

对于Spring MVC 该方法添加到 Controller 类上进行处理。

