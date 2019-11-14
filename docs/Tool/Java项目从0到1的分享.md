# Java项目从0到1的分享

## 前言

## 项目

1.  明确你的项目到底是做什么的，有哪些功能。 
2.  明确你的项目的整体架构，在面试的时候能够清楚地画给面试官看并且清楚地指出从哪里调用到哪里、使用什么方式调用。 
3.  明确你的模块在整个项目中所处的位置及作用。 
4. 明确你的模块用到了哪些技术，更好一些的可以再了解一下整个项目用到了哪些技术。

## 会议

开会或者开小会，开完后都必须写个会议纪要

## 分享

### MVC 入参

```
排序方式、分页参数、具体查询条件(Object)
```

#### 排序方式

数据排序中无特殊要求，以更新时间降序排序进行排序

```java
public class OrderParameter implements Serializable {
    
       private static final long serialVersionUID = 1L;
       /** sort=orderBy|direction */
       private String sort;
       /** 根据什么排序*/
       private String orderBy;
       /** 降序还是升序（desc/asc) */
       private String direction;
      // 省略 getter/setter和toString 方法
}  
```

#### 分页参数



```java
public class PageParameter implements Serializable{
	private static final long serialVersionUID = 1L;
	/** 默认查询页数量为10 */
	public static final int DEFAULT_PAGE_SIZE = 10;
	/** 默认当前页码为第一页 */
	public static final int DEFAULT_CUR_PAGE = 1;
    /** 查询页数量*/
    private int pageSize;
    /** 当前页码 */
    private int curPage;
    // 省略 getter/setter和toString 方法
}
```

### MVC 出参

#### CommonResponse

```java
public class CommonResponse {
    /**结果*/
    private boolean status;
    /**错误码，当isSuccess=false，需要设置值*/
    private ErrorCode errorCode;
    /**信息，当isSuccess=false，存储错误信息，也可以存储其他信息*/
    private String message;
    /**返回数据，一般在isSuccess=true时使用*/
    private Object data;
    // 省略 getter/setter和toString 方法
    /** 可以值提供该构造方法，通过建造者模式创建 */
    public CommonResponse(boolean status, ErrorCode errorCode, String msg, Object data) {
        this.status = status;
        this.errorCode = errorCode;
        this.msg = msg;
        this.data = data;
    }
}
```

#### ErrorCode

```java
public enum ErrorCode {
    /**
     * code 设计为5为数据值，其中前两位为模块id，后三位自定义错误信息
     * 其中前两位为 00 属于系统错误码
     * 10 属于权限问题
     */
    SUCCESS("00000", "成功"),
    SYSTEM_ERROR("00001", "系统错误")
    ;

    private String code;
    private String errorMsg;

    ErrorCode(String code, String errorMsg) {
        this.code = code;
        this.errorMsg = errorMsg;
    }
}
```

#### ResponseBuilder

```java
public class ResponseBuilder {
    /** 返回成功，无数据返回 */
    public static CommonResponse buildSuccessResponse() {
        return new CommonResponse(true, null, null, null);
    }
    /** 返回成功，有数据返回 */
    public static CommonResponse buildSuccessResponse(Object data) {
        return new CommonResponse(true, null, null, data);
    }
    /** 返回失败，无数据返回 */
    public static CommonResponse buildFailResponse(String message, ErrorCode errorCode) {
        return new CommonResponse(false, errorCode, message, null);
    }
    /** 返回失败，有数据返回 */
    public static CommonResponse buildFailResponse(String message, ErrorCode errorCode, Object data) {
        return new CommonResponse(false, errorCode, message, data);
    }
    /** 系统错误 */
    public static CommonResponse buildFailResponse(String message) {
        return new CommonResponse(false, ErrorCode.SYSTEM_ERROR, message, null);
    }
}
```

### 数据库表设计

```
数据表中的字段，除非要求设置为非空，否则不要设置成非空
除了必要字段外，还需要添加一下五个字段
status: 该记录的状态（0 表示未删除可以查询，1 表示已删除不可查询）
create_time：数据创建时间
creator：数据创建人
update_time：数据更新时间
updater：数据更新人
```



