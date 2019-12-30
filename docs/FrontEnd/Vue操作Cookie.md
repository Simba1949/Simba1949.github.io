# Vue操作Cookie

## 前言

博客书

版本说明

```properties

```

相关链接

* 工具地址：https://www.npmjs.com/package/js-cookie
* js-cookie 下载地址：https://github.com/js-cookie/js-cookie/blob/latest/src/js.cookie.js

## 实战演练

### 引入

js-cookie 下载地址：https://github.com/js-cookie/js-cookie/blob/latest/src/js.cookie.js

```shell
# 本地引入
<script src="/path/to/js.cookie.js"></script>
# CDN 引入
<script src="https://cdn.jsdelivr.net/npm/js-cookie@2/src/js.cookie.min.js"></script>
# NPM 安装
npm install js-cookie --save
```

### Vue 安装后引入

```shell
# 引入
import Cookies from 'js-cookie'
```

### 操作

参考：https://www.npmjs.com/package/js-cookie

Create a cookie, valid across the entire site:

```
Cookies.set('name', 'value');
```

Create a cookie that expires 7 days from now, valid across the entire site:

```
Cookies.set('name', 'value', { expires: 7 });
```

Create an expiring cookie, valid to the path of the current page:

```
Cookies.set('name', 'value', { expires: 7, path: '' });
```

Read cookie:

```
Cookies.get('name'); // => 'value'
Cookies.get('nothing'); // => undefined
```

Read all visible cookies:

```
Cookies.get(); // => { name: 'value' }
```

*Note: It is not possible to read a particular cookie by passing one of the cookie attributes (which may or may not have been used when writing the cookie in question):*

```
Cookies.get('foo', { domain: 'sub.example.com' }); // `domain` won't have any effect...!
```

The cookie with the name `foo` will only be available on `.get()` if it's visible from where the code is called; the domain and/or path attribute will not have an effect when reading.

Delete cookie:

```
Cookies.remove('name');
```

Delete a cookie valid to the path of the current page:

```
Cookies.set('name', 'value', { path: '' });
Cookies.remove('name'); // fail!
Cookies.remove('name', { path: '' }); // removed!
```

*IMPORTANT! When deleting a cookie and you're not relying on the [default attributes](https://www.npmjs.com/package/js-cookie#cookie-attributes), you must pass the exact same path and domain attributes that were used to set the cookie:*

```
Cookies.remove('name', { path: '', domain: '.yourdomain.com' });
```

*Note: Removing a nonexistent cookie does not raise any exception nor return any value.*





