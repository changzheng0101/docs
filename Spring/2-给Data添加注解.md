# 数据校验

要检验数据的合法性，主要有三种方式：

* 在web视图中进行校验
* 给Data添加注解进行校验
* 在Controller的具体类中进行检查

## 添加注解

```java
package tacos;
import java.util.List;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;
import lombok.Data;
@Data
public class Taco {
 @NotNull
 @Size(min=5, message="Name must be at least 5 characters long")
 private String name;
 @Size(min=1, message="You must choose at least 1 ingredient")
 private List<String> ingredients;
}
```

```java
package tacos;
import javax.validation.constraints.Digits;
import javax.validation.constraints.Pattern;
import org.hibernate.validator.constraints.CreditCardNumber;
import javax.validation.constraints.NotBlank;
import lombok.Data;
@Data
public class Order {
 @NotBlank(message="Name is required")
 private String name;
 @NotBlank(message="Street is required")
 private String street;
 @NotBlank(message="City is required")
 private String city;
 @NotBlank(message="State is required")
 private String state;
 @NotBlank(message="Zip code is required")
 private String zip;
  //信用卡
 @CreditCardNumber(message="Not a valid credit card number")
 private String ccNumber;
 @Pattern(regexp="^(0[1-9]|1[0-2])([\\/])([1-9][0-9])$",
 message="Must be formatted MM/YY")
 private String ccExpiration;
 // 必须为三位数
 @Digits(integer=3, fraction=0, message="Invalid CVV")
 private String ccCVV;
}
```

需要在Controller中添加`@valid`校验

```java
@PostMapping
public String processOrder(@Valid Order order, Errors errors) {
 if (errors.hasErrors()) {
 return "orderForm";
 }
 log.info("Order submitted: " + order);
 return "redirect:/";
}
```

