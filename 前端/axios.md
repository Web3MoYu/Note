# Axios的传参方式

## 1.请求头中携带数据

```js
const resp = await axios.post(
    "/api/a3",
    {},
    {
        headers: {
            Authorization: "auth",
        },
    }
);
```

```http
Accept: application/json, text/plain, */*
Accept-Encoding:gzip, deflate, br
Accept-Language:zh-CN,zh;q=0.9,en;q=0.8
## 这里
Authorization: auth 
Connection:keep-alive
Content-Length:2
Content-Type:application/json
Cookie:Webstorm-8572547b=64365f23-15c2-4a35-a8a7-ea87e4eb9f4c
Host:
localhost:7070
Origin:
http://localhost:7070
Referer:
http://localhost:7070/
Sec-Ch-Ua:"Google Chrome";v="113", "Chromium";v="113", "Not-A.Brand";v="24"
Sec-Ch-Ua-Mobile:?0
Sec-Ch-Ua-Platform:"Windows"
Sec-Fetch-Dest:empty
Sec-Fetch-Mode:cors
Sec-Fetch-Site:same-origin
User-Agent:Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
```

## 2.发送请求时携带查询参数?name=xxx&age=xxx

```js
const name = encodeURIComponent("&&&&");
const age = 18;
const resp = await axios.post(`/api/a4?name=${name}&age=${age}`);
```

**url**

```
http://localhost:7070/api/a4?name=%26%26%26%26&age=18
```

**查询字符串参数**

```
name=%26%26%26%26&age=18

name: &&&&
age: 18
```

**Payload**

```

```

## 3.发送请求携带查询参数

```js
async sendReqAsync3() {
    const resp = await axios.post(
        "/api/a4",
        {},
        {
            params: {
                name: "&&&",
                age: 18,
            },
        }
    );
```

**url**

```
http://localhost:7070/api/a4?name=%26%26%26&age=18
```

**查询字符串参数**

```
name=%26%26%26%26&age=18

name: %26%26%26
age: 18
```

**Payload**

```
{}
```

## 4.请求体发送数据，格式为urlencoded

```js
async sendReqAsync4() {
    const params = new URLSearchParams();
    params.append("name", "张三");
    params.append("age", 24);
    const resp = axios.post("/api/a4", params);
    console.log(resp);
},
```



**url**

```
http://localhost:7070/api/a4
```

**表单参数**

```
name=%E5%BC%A0%E4%B8%89&age=24

name: 张三
age: 24
```

**Payload**

```

```

## 4.请求体发送数据，格式为multipart

```js
async sendReqAsync5() {
    const params = new FormData();
    params.append("name", "李四");
    params.append("age", 30);
    const resp = await axios.post("/api/a4", params);
    console.log(resp);
},
```



**url**

```
http://localhost:7070/api/a4
```

**表单参数**

```
------WebKitFormBoundaryDB39JtuqqN7DAS7f
Content-Disposition: form-data; name="name"

李四
------WebKitFormBoundaryDB39JtuqqN7DAS7f
Content-Disposition: form-data; name="age"

30
------WebKitFormBoundaryDB39JtuqqN7DAS7f--

name: 李四
age: 30
```

**Payload**

```

```

## 5.请求体发送数据，格式为urlencoded

```js
async sendReqAsync6() {
    const resp = await axios.post("/api/a5json", {
        name : '王五',
        age : 50
    });
},
```

**url**

```
http://localhost:7070/api/a5json
```

**表单参数**

```

```

**Payload**

```
{"name":"王五","age":50}
```

