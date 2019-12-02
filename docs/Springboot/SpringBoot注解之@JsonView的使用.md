# SpringBoot注解之@JsonView的使用

## 前言

博客书

版本说明

```properties
platform-bom=Cairo-SR7
```

相关链接：

* spring io 官网：https://spring.io/projects/platform

## 实战演练

@JsonView 使用步骤

1. 使用接口来声明多个视图
2. 在值对象的get方法山指定视图
3. 在 Controller 方法上指定视图

### User 类

```java
package top.simba1949.common;

import com.fasterxml.jackson.annotation.JsonView;
import lombok.Setter;
import lombok.ToString;

/**
 * @Author Theodore
 * @Date 2019/12/2 15:32
 */
@Setter
@ToString
public class User {
    /**
     * 1. 使用接口来声明多个视图
     */
    public interface UserSimpleView{}
    public interface UserDetailView extends UserSimpleView{}

    private String username;
    private String password;

    /**
     * 2. 在值对象的get方法山指定视图
     */
    @JsonView(UserSimpleView.class)
    public String getUsername() {
        return username;
    }

    @JsonView(UserDetailView.class)
    public String getPassword() {
        return password;
    }
}
```

### Controller

```java
package top.simba1949.controller;

import com.fasterxml.jackson.annotation.JsonView;
import org.springframework.data.domain.Pageable;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import top.simba1949.common.User;

import java.util.ArrayList;
import java.util.List;

/**
 * @Author Theodore
 * @Date 2019/12/2 15:29
 */
@RestController
@RequestMapping("user")
public class UserController {
    /**
     *
     * @param username
     * @param pageable org.springframework.data.domain.Pageable 分页对象
     * @return
     * 返回结果如下 [{"username":null},{"username":null},{"username":null}]
     */
    @GetMapping("list1")
    @JsonView(User.UserSimpleView.class)
    public List<User> query(String username, Pageable pageable){
        List<User> users = new ArrayList<>();
        users.add(new User());
        users.add(new User());
        users.add(new User());
        return users;
    }

    /**
     *
     * @param username
     * @return
     *  返回结果如下 [{"username":null,"password":null},{"username":null,"password":null},{"username":null,"password":null}]
     */
    @GetMapping("list2")
    @JsonView(User.UserDetailView.class)
    public List<User> query(String username){
        List<User> users = new ArrayList<>();
        users.add(new User());
        users.add(new User());
        users.add(new User());
        return users;
    }
}
```

