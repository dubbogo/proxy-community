## 一、简介

dubbogoproxy 项目的测试例子在项目的 samples 目录下及 [dubbo-go-proxy-samples](https://github.com/dubbogo/dubbo-go-proxy-samples)，通过这些测试的例子来验证 proxy 功能的可用性。

>  在每次版本发布之前都需要验证测试项目里面的所有测试场景

## 二、Dubbo 测试例子

### 2.1 Dubbo Query

表单查询模式

#### 2.1.1 API 配置

```yml
name: proxy
description: proxy sample
resources:
  - path: '/api/v1/test-dubbo/userByName'
    type: restful
    description: user
    filters:
      - filter0
    methods:
      - httpVerb: GET
        onAir: true
        timeout: 1000ms
        inboundRequest:
          requestType: http
          queryStrings:
            - name: name
              required: true
        integrationRequest:
          requestType: dubbo
          mappingParams:
            - name: queryStrings.name
              mapTo: 0
          applicationName: "UserService"
          interface: "com.dubbogo.proxy.UserService"
          method: "GetUserByName"
          paramTypes: [ "java.lang.String" ]
          group: "test"
          version: 1.0.0
          clusterName: "test_dubbo"
  - path: '/api/v1/test-dubbo/userByNameAndAge'
    type: restful
    description: user
    filters:
      - filter0
    methods:
      - httpVerb: GET
        onAir: true
        timeout: 1000ms
        inboundRequest:
          requestType: http
          queryStrings:
            - name: name
              required: true
            - name: age
              required: true
        integrationRequest:
          requestType: dubbo
          mappingParams:
            - name: queryStrings.name
              mapTo: 0
            - name: queryStrings.age
              mapTo: 1
          applicationName: "UserService"
          interface: "com.dubbogo.proxy.UserService"
          method: "GetUserByNameAndAge"
          paramTypes: [ "java.lang.String","java.lang.Integer" ]
          group: "test"
          version: 1.0.0
          clusterName: "test_dubbo"
  - path: '/api/v1/test-dubbo/userByCode'
    type: restful
    description: user
    filters:
      - filter0
    methods:
      - httpVerb: GET
        onAir: true
        timeout: 1000ms
        inboundRequest:
          requestType: http
          queryStrings:
            - name: code
              required: true
        integrationRequest:
          requestType: dubbo
          mappingParams:
            - name: queryStrings.code
              mapTo: 0
          applicationName: "UserService"
          interface: "com.dubbogo.proxy.UserService"
          method: "GetUserByCode"
          paramTypes: [ "java.lang.Integer" ]
          group: "test"
          version: 1.0.0
          clusterName: "test_dubbo"
```

#### 2.1.2 请求效果

##### 2.1.2.1 单个参数 string 类型

```bash
GET http://localhost:8888/api/v1/test-dubbo/userByName?name=tc

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 08:44:28 GMT
Content-Length: 82

{
  "age": 18,
  "code": 1,
  "iD": "0001",
  "name": "tc",
  "time": "2020-12-20T15:51:36.333+08:00"
}
```

##### 2.1.2.2 单个参数 int 类型

```bash
GET http://localhost:8888/api/v1/test-dubbo/userByCode?code=1

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 08:46:15 GMT
Content-Length: 82

{
  "age": 18,
  "code": 1,
  "iD": "0001",
  "name": "tc",
  "time": "2020-12-20T15:51:36.333+08:00"
}
```

##### 2.1.2.3 多个参数 int 和 string 类型

```bash
GET http://localhost:8888/api/v1/test-dubbo/userByNameAndAge?name=tc&age=99

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 08:43:16 GMT
Content-Length: 82

{
  "age": 18,
  "code": 1,
  "iD": "0001",
  "name": "tc",
  "time": "2020-12-20T15:51:36.333+08:00"
}
```

### 2.2 Dubbo URI

#### 2.2.1 API 配置

```yaml
name: proxy
description: proxy sample
resources:
  - path: '/api/v1/test-dubbo/user/name/:name'
    type: restful
    description: user
    filters:
      - filter0
    methods:
      - httpVerb: GET
        onAir: true
        timeout: 1000ms
        inboundRequest:
          requestType: http
          uri:
            - name: name
              required: true
        integrationRequest:
          requestType: dubbo
          mappingParams:
            - name: uri.name
              mapTo: 0
          applicationName: "UserProvider"
          interface: "com.dubbogo.proxy.UserService"
          method: "GetUserByName"
          paramTypes: [ "string" ]
          group: "test"
          version: 1.0.0
          clusterName: "test_dubbo"
  - path: '/api/v1/test-dubbo/user/code/:code'
    type: restful
    description: user
    filters:
      - filter0
    methods:
      - httpVerb: GET
        onAir: true
        timeout: 1000ms
        inboundRequest:
          requestType: http
          uri:
            - name: code
              required: true
        integrationRequest:
          requestType: dubbo
          mappingParams:
            - name: uri.code
              mapTo: 0
          applicationName: "UserProvider"
          interface: "com.dubbogo.proxy.UserService"
          method: "GetUserByCode"
          paramTypes: [ "int" ]
          group: "test"
          version: 1.0.0
          clusterName: "test_dubbo"
  - path: '/api/v1/test-dubbo/user/name/:name/age/:age'
    type: restful
    description: user
    filters:
      - filter0
    methods:
      - httpVerb: GET
        onAir: true
        timeout: 1000ms
        inboundRequest:
          requestType: http
          uri:
            - name: name
              required: true
            - name: age
              required: true
        integrationRequest:
          requestType: dubbo
          mappingParams:
            - name: uri.name
              mapTo: 0
            - name: uri.age
              mapTo: 1
          applicationName: "UserProvider"
          interface: "com.dubbogo.proxy.UserService"
          method: "GetUserByNameAndAge"
          paramTypes: [ "string", "int" ]
          group: "test"
          version: 1.0.0
          clusterName: "test_dubbo"
```

#### 2.2.2 请求效果

##### 2.2.2.1 单个参数 string 类型

```bash
GET http://localhost:8888/api/v1/test-dubbo/user/name/tc

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 09:05:31 GMT
Content-Length: 82

{
  "age": 18,
  "code": 1,
  "iD": "0001",
  "name": "tc",
  "time": "2020-12-20T17:04:23.314+08:00"
}
```

##### 2.2.2.2 单个参数 int 类型

```bash
GET http://localhost:8888/api/v1/test-dubbo/user/code/1

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 09:07:23 GMT
Content-Length: 82

{
  "age": 18,
  "code": 1,
  "iD": "0001",
  "name": "tc",
  "time": "2020-12-20T17:04:23.314+08:00"
}
```

##### 2.2.2.3 多个参数 int 和 string 类型

```bash
GET http://localhost:8888/api/v1/test-dubbo/user/name/tc/age/99

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 09:07:30 GMT
Content-Length: 82

{
  "age": 18,
  "code": 1,
  "iD": "0001",
  "name": "tc",
  "time": "2020-12-20T17:04:23.314+08:00"
}
```

### 2.3 Dubbo Body

> 这里有个注意点，返回 error 的话会重试，如果你 返回没有 error，则不会重试

#### 2.3.1 API 配置

```bash
name: proxy
description: proxy sample
resources:
  - path: '/api/v1/test-dubbo/user'
    type: restful
    description: user
    methods:
      - httpVerb: POST
        onAir: true
        timeout: 10s
        inboundRequest:
          requestType: http
        integrationRequest:
          requestType: dubbo
          mappingParams:
            - name: requestBody._all
              mapTo: 0
          applicationName: "UserProvider"
          interface: "com.dubbogo.proxy.UserService"
          method: "CreateUser"
          paramTypes: [ "object" ]
          group: "test"
          version: 1.0.0
          clusterName: "test_dubbo"
      - httpVerb: PUT
        onAir: true
        timeout: 10s
        inboundRequest:
          requestType: http
        integrationRequest:
          requestType: dubbo
          mappingParams:
            - name: requestBody._all
              mapTo: 0
          applicationName: "UserProvider"
          interface: "com.dubbogo.proxy.UserService"
          method: "UpdateUser"
          paramTypes: [ "object" ]
          group: "test"
          version: 1.0.0
          clusterName: "test_dubbo"
```

#### 2.3.2 请求效果

##### 2.3.2.1 创建

```bash
POST http://localhost:8888/api/v1/test-dubbo/user

{
  "id": "0003",
  "code": 3,
  "name": "dubbogo",
  "age": 99
}

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 12:05:42 GMT
Content-Length: 48

{
  "age": 99,
  "code": 3,
  "iD": "0003",
  "name": "dubbogo"
}
```

##### 2.3.2.2 单参数更新

- 成功

```bash
PUT http://localhost:8888/api/v1/test-dubbo/user

{
  "id": "0003",
  "code": 3,
  "name": "dubbogo",
  "age": 18
}

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 11:58:13 GMT
Content-Length: 4

true
```

- 失败

```bash
PUT http://localhost:8888/api/v1/test-dubbo/user

HTTP/1.1 503 Service Unavailable
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 11:59:50 GMT
Content-Length: 23

{
  "message": "not found"
}
```

##### 2.3.2.3 多参数更新

```bash
PUT http://localhost:8888/api/v1/test-dubbo/user2

{
  "name": "tc",
  "user": {
    "id": "0001",
    "code": 1,
    "name": "tc",
    "age": 99
  }
}

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 14:16:21 GMT
Content-Length: 4

true
```

### 2.4 Dubbo Proxy

> 减少接口配置，直接把调用的目标通过参数传递

#### 2.4.1 API 配置

```yaml
name: proxy
description: proxy sample
resources:
  - path: '/api/v1/test-dubbo/:application/:interface'
    type: restful
    description: common
    methods:
      - httpVerb: POST
        onAir: true
        timeout: 100s
        inboundRequest:
          requestType: http
        integrationRequest:
          requestType: dubbo
          mappingParams:
            - name: requestBody.values
              mapTo: 0
              opt:
                open: true
                usable: true
                name: values
            - name: requestBody.types
              mapTo: 1
              opt:
                open: true
                name: types
            - name: uri.application
              mapTo: 2
              opt:
                open: true
                name: application
            - name: uri.interface
              mapTo: 3
              opt:
                open: true
                name: interface
            - name: queryStrings.method
              mapTo: 4
              opt:
                open: true
                name: method
            - name: queryStrings.group
              mapTo: 5
              opt:
                open: true
                name: group
            - name: queryStrings.version
              mapTo: 6
              opt:
                open: true
                name: version
          paramTypes: ["object", "object", "string", "string", "string", "string", "string"]
          toParamTypes: ["string"]
          clusterName: "test_dubbo"

```

#### 2.4.2 请求效果

##### 2.4.2.1 单个参数 string 类型

```bash
POST http://localhost:8888/api/v1/test-dubbo/UserService/com.dubbogo.proxy.UserService?group=test&version=1.0.0&method=GetUserByName

{
  "types": [
    "string"
  ],
  "values": "tc"
}

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 13:49:03 GMT
Content-Length: 82

{
  "age": 18,
  "code": 1,
  "iD": "0001",
  "name": "tc",
  "time": "2020-12-20T20:54:38.746+08:00"
}
```

##### 2.4.2.2 单个参数 int 类型

```bash
POST http://localhost:8888/api/v1/test-dubbo/UserService/com.dubbogo.proxy.UserService?group=test&version=1.0.0&method=GetUserByCode

{
  "types": [
    "int"
  ],
  "values": 1
}

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 13:51:37 GMT
Content-Length: 82

{
  "age": 18,
  "code": 1,
  "iD": "0001",
  "name": "tc",
  "time": "2020-12-20T20:54:38.746+08:00"
}
```

##### 2.4.2.3 多个参数

```bash
POST http://localhost:8888/api/v1/test-dubbo/UserService/com.dubbogo.proxy.UserService?group=test&version=1.0.0&method=UpdateUserByName

{
  "types": [
    "string",
    "body"
  ],
  "values": [
    "tc",
    {
      "id": "0001",
      "code": 1,
      "name": "tc",
      "age": 15
    }
  ]
}

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 13:53:33 GMT
Content-Length: 4

true
```

### 2.5 Dubbo Mix

#### 2.5.1 API 配置

```yaml
name: proxy
description: proxy sample
resources:
  - path: '/api/v1/test-dubbo/user/:name'
    type: restful
    description: user
    filters:
      - filter0
    methods:
      - httpVerb: GET
        onAir: true
        timeout: 1000ms
        inboundRequest:
          requestType: http
        integrationRequest:
          requestType: dubbo
          mappingParams:
            - name: uri.name
              mapTo: 0
            - name: queryStrings.age
              mapTo: 1
          applicationName: "UserService"
          interface: "com.dubbogo.proxy.UserService"
          method: "GetUserByNameAndAge"
          paramTypes: [ "string", "int" ]
          group: "test"
          version: 1.0.0
          clusterName: "test_dubbo"
      - httpVerb: PUT
        onAir: true
        timeout: 1000ms
        inboundRequest:
          requestType: http
        integrationRequest:
          requestType: dubbo
          mappingParams:
            - name: uri.name
              mapTo: 0
            - name: requestBody._all
              mapTo: 1
          applicationName: "UserService"
          interface: "com.dubbogo.proxy.UserService"
          method: "UpdateUserByName"
          paramTypes: [ "string", "object" ]
          group: "test"
          version: 1.0.0
          clusterName: "test_dubbo"
  - path: '/api/v1/test-dubbo/user'
    type: restful
    description: user
    filters:
      - filter0
    methods:
      - httpVerb: PUT
        onAir: true
        timeout: 1000ms
        inboundRequest:
          requestType: http
        integrationRequest:
          requestType: dubbo
          mappingParams:
            - name: queryStrings.name
              mapTo: 0
            - name: requestBody._all
              mapTo: 1
          applicationName: "UserService"
          interface: "com.dubbogo.proxy.UserService"
          method: "UpdateUserByName"
          paramTypes: [ "string", "object" ]
          group: "test"
          version: 1.0.0
          clusterName: "test_dubbo"
```

#### 2.5.2 请求效果

##### 2.5.2.1 多个查询查询，分别来自 query 和 uri

```bash
GET http://localhost:8888/api/v1/test-dubbo/user/tc?age=99

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 13:59:06 GMT
Content-Length: 82

{
  "age": 15,
  "code": 1,
  "iD": "0001",
  "name": "tc",
  "time": "2020-12-20T20:54:38.746+08:00"
}
```

##### 2.5.2.2  多个参数查询，分别来自 body 和 query

```bash
PUT http://localhost:8888/api/v1/test-dubbo/user?name=tc

{
  "id": "0001",
  "code": 1,
  "name": "tc",
  "age": 55
}

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 14:02:31 GMT
Content-Length: 4

true
```

##### 2.5.2.3 多个参数查询，分别来自 body 和 uri

```bash
PUT http://localhost:8888/api/v1/test-dubbo/user/tc

{
  "id": "0001",
  "code": 1,
  "name": "tc",
  "age": 66
}

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Sun, 20 Dec 2020 14:06:29 GMT
Content-Length: 4

true
```

## 三、http 测试例子

