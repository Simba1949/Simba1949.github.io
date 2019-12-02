# SpringBootTest 学习笔记

## 前言

博客书

版本说明

```properties
platform-bom=Cairo-SR7
```

相关链接

* jsonPath github 链接：https://github.com/json-path/JsonPath
* spring io 官网：https://spring.io/projects/platform

## 实战演练

```java
package top.simba1949.web.controller;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.ResultActions;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.result.MockMvcResultMatchers;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

/**
 * @RunWith(SpringRunner.class) 告诉JUnit使用Spring的测试支持
 * @SpringBootTest 使用Spring Boot支持的引导,需要加载springboot的配置文件
 *
 * @Author Theodore
 * @Date 2019/12/2 15:15
 */
@RunWith(SpringRunner.class)
@SpringBootTest
public class UserControllerTest {
    /**
     * 伪造 MVC 环境
     */
    @Autowired
    private WebApplicationContext webApplicationContext;

    private MockMvc mockMvc;

    @Before
    public void init(){
        mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext).build();
    }

    /**
     *  MockMvcRequestBuilders 创建请求方式
     * @throws Exception
     */
    @Test
    public void whenQuerySuccess() throws Exception {
        ResultActions resultActions = mockMvc.perform(
                // 创建一个请求
                MockMvcRequestBuilders.get("/user")
                        // 添加 contentType 信息
                        .contentType(MediaType.APPLICATION_JSON_UTF8)
                        // 添加请求参数
                        .param("username", "test")
        );

        resultActions
                // 执行结果期望
                .andExpect(MockMvcResultMatchers.status().isOk())
                // jsonPath 参考 https://github.com/json-path/JsonPath
                .andExpect(MockMvcResultMatchers.jsonPath("$.length()").value(3));
    }
}
```
