# 接口文档

Base URLs: /api/zdmj

# Authentication

- HTTP Authentication, scheme: bearer
- jwt token
- 除了以下接口，其他接口均需要登录认证
  - `POST /api/zdmj/users`（注册）
  - `POST /api/zdmj/users/login`（登录）
  - `POST /api/zdmj/users/verification-codes`（发送验证码）
  - `PUT /api/zdmj/users/password`（重置密码）
  - `GET /api/zdmj/users/validation/**`（用户名/邮箱校验）

## 统一错误码与状态码映射


| HTTP状态 | 场景                             | 示例业务码                              |
| ------ | ------------------------------ | ---------------------------------- |
| 400    | 参数校验失败、请求参数/类型错误、以及非“不存在”类业务错误 | `1001`、`2001`、`2002`、`2003`、`2007` |
| 401    | 未登录或 JWT 无效（安全层拦截）             | `401`                              |
| 403    | 已登录但无权操作他人资源                   | `1003`                             |
| 404    | 业务错误消息包含“不存在”（如用户/教育经历/岗位不存在）  | `2006`、`6006`、`8201`               |


### 统一错误返回示例

#### 400 参数错误（业务层）

```json
{
  "code": 1001,
  "msg": "参数校验失败: 用户名不能为空",
  "data": null
}
```

#### 401 未认证（安全层）

```json
{
  "code": 401,
  "message": "Unauthorized"
}
```

#### 403 无权限（安全层）

```json
{
  "code": 403,
  "message": "Access Denied"
}
```

#### 404 资源不存在（业务层）

```json
{
  "code": 8201,
  "msg": "岗位不存在",
  "data": null
}
```

#### 400 业务失败（业务层）

```json
{
  "code": 2001,
  "msg": "用户名已存在",
  "data": null
}
```

# 用户认证

## POST 创建用户（用户注册）

POST /users

> Body 请求参数

```json
{
  "username": "string",
  "password": "string",
  "email": "user@example.com",
  "verificationCode": "string"
}
```

### 请求参数


| 名称   | 位置   | 类型                                        | 必选  | 说明   |
| ---- | ---- | ----------------------------------------- | --- | ---- |
| body | body | [UserRegisterDTO](#schemauserregisterdto) | 否   | none |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": {
    "id": 0,
    "username": "string",
    "email": "string",
    "name": "string",
    "phone": "string",
    "website": "string"
  }
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## GET 根据ID查询用户信息

GET /users/{id}

### 请求参数


| 名称  | 位置   | 类型      | 必选  | 说明   |
| --- | ---- | ------- | --- | ---- |
| id  | path | integer | 是   | 用户ID |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": {
    "id": 0,
    "username": "string",
    "email": "string",
    "name": "string",
    "phone": "string",
    "website": "string"
  }
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## POST 用户登录

POST /users/login

> Body 请求参数

```json
{
  "usernameOrEmail": "string",
  "password": "string"
}
```

### 请求参数


| 名称   | 位置   | 类型                                  | 必选  | 说明   |
| ---- | ---- | ----------------------------------- | --- | ---- |
| body | body | [UserLoginDTO](#schemauserlogindto) | 否   | none |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": {
    "token": "string",
    "user": {
      "id": 0,
      "username": "string",
      "email": "string",
      "name": "string",
      "phone": "string",
      "website": "string"
    }
  }
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## POST 发送验证码

POST /users/verification-codes

### 请求参数


| 名称    | 位置    | 类型     | 必选  | 说明   |
| ----- | ----- | ------ | --- | ---- |
| email | query | string | 是   | 邮箱地址 |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": null
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## PUT 重置密码（忘记密码时使用）

PUT /users/password

> Body 请求参数

```json
{
  "email": "user@example.com",
  "verificationCode": "string",
  "newPassword": "string"
}
```

### 请求参数


| 名称   | 位置   | 类型                                                  | 必选  | 说明   |
| ---- | ---- | --------------------------------------------------- | --- | ---- |
| body | body | [UserResetPasswordDTO](#schemauserresetpassworddto) | 否   | none |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": null
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## PUT 更新当前登录用户的基本信息

PUT /users/me

> Body 请求参数

```json
{
  "name": "string",
  "phone": "string",
  "website": "string"
}
```

### 请求参数


| 名称   | 位置   | 类型                                    | 必选  | 说明   |
| ---- | ---- | ------------------------------------- | --- | ---- |
| body | body | [UserUpdateDTO](#schemauserupdatedto) | 否   | none |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": {
    "id": 0,
    "username": "string",
    "email": "string",
    "name": "string",
    "phone": "string",
    "website": "string"
  }
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## GET 验证用户名是否存在

GET /users/validation/username

### 请求参数


| 名称       | 位置    | 类型     | 必选  | 说明  |
| -------- | ----- | ------ | --- | --- |
| username | query | string | 是   | 用户名 |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": true
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## GET 验证邮箱是否存在

GET /users/validation/email

### 请求参数


| 名称    | 位置    | 类型     | 必选  | 说明  |
| ----- | ----- | ------ | --- | --- |
| email | query | string | 是   | 邮箱  |


> 返回示例

> 200 Response

```json
{
  
  "code": 0,
  "msg": "string",
  "data": true
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## 用户认证相关数据模型

- 请求模型：`UserRegisterDTO`、`UserLoginDTO`、`UserResetPasswordDTO`、`UserUpdateDTO`
- 响应模型：`UserDTO`、`UserLoginResponseDTO`、`Result`

### 模型索引

- [UserRegisterDTO](#schemauserregisterdto)
- [UserLoginDTO](#schemauserlogindto)
- [UserResetPasswordDTO](#schemauserresetpassworddto)
- [UserUpdateDTO](#schemauserupdatedto)
- [UserDTO](#schemauserdto)
- [UserLoginResponseDTO](#schemauserloginresponsedto)
- [Result](#schemaresult)
- [Result](#schemaresult)
- [Result](#schemaresult)
- [Result](#schemaresult)

### 数据模型（内联）

#### UserRegisterDTO（注册请求）


| 字段               | 类型            | 约束             |
| ---------------- | ------------- | -------------- |
| username         | string        | 必填，长度 3-50     |
| password         | string        | 必填，长度 6-20     |
| email            | string(email) | 必填，邮箱格式，最大 100 |
| verificationCode | string        | 必填，固定 6 位      |


#### UserLoginDTO（登录请求）


| 字段              | 类型     | 约束  |
| --------------- | ------ | --- |
| usernameOrEmail | string | 必填  |
| password        | string | 必填  |


#### UserResetPasswordDTO（重置密码请求）


| 字段               | 类型            | 约束         |
| ---------------- | ------------- | ---------- |
| email            | string(email) | 必填，邮箱格式    |
| verificationCode | string        | 必填，固定 6 位  |
| newPassword      | string        | 必填，长度 6-50 |


#### UserUpdateDTO（更新资料请求）


| 字段      | 类型     | 约束        |
| ------- | ------ | --------- |
| name    | string | 可选，最大 100 |
| phone   | string | 可选，最大 50  |
| website | string | 可选，最大 255 |


## 用户认证异常场景


| 接口                    | 场景       | HTTP状态 | 业务码  |
| --------------------- | -------- | ------ | ---- |
| `POST /users`         | 用户名已存在   | 400    | 2001 |
| `POST /users`         | 邮箱已被注册   | 400    | 2002 |
| `POST /users`         | 验证码错误或过期 | 400    | 2003 |
| `POST /users/login`   | 用户名或密码错误 | 400    | 2005 |
| `GET /users/{id}`     | 用户不存在    | 404    | 2006 |
| `PUT /users/password` | 邮箱未注册    | 400    | 2007 |
| `PUT /users/password` | 验证码错误或过期 | 400    | 2003 |
| `PUT /users/me`       | 未登录      | 401    | 401  |
| `PUT /users/me`       | 用户不存在    | 404    | 2006 |


# 教育经历控制器

## POST 添加教育经历

POST /educations

使用 CreateGroup 验证组，所有必填字段都需要验证

> Body 请求参数

```json
{
  "id": 0,
  "school": "string",
  "major": "string",
  "degree": 1,
  "startDate": "string",
  "endDate": "string",
  "visible": true,
  "gpa": "string"
}
```

### 请求参数


| 名称   | 位置   | 类型                                                        | 必选  | 说明   |
| ---- | ---- | --------------------------------------------------------- | --- | ---- |
| body | body | [EducationDTOCreateGroup](#schemaeducationdtocreategroup) | 否   | none |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": {
    "id": 0,
    "userId": 0,
    "school": "string",
    "major": "string",
    "degree": 0,
    "startDate": "string",
    "endDate": "string",
    "visible": true,
    "gpa": "string"
  }
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## PUT 更新教育经历

PUT /educations

使用 UpdateGroup 验证组，只验证提供的字段（非空字段才验证）
ID 包含在请求体中，不使用路径变量

> Body 请求参数

```json
{
  "id": 0,
  "school": "string",
  "major": "string",
  "degree": 1,
  "startDate": "string",
  "endDate": "string",
  "visible": true,
  "gpa": "string"
}
```

### 请求参数


| 名称   | 位置   | 类型                                                        | 必选  | 说明   |
| ---- | ---- | --------------------------------------------------------- | --- | ---- |
| body | body | [EducationDTOUpdateGroup](#schemaeducationdtoupdategroup) | 否   | none |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": {
    "id": 0,
    "userId": 0,
    "school": "string",
    "major": "string",
    "degree": 0,
    "startDate": "string",
    "endDate": "string",
    "visible": true,
    "gpa": "string"
  }
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## GET 根据用户ID查询所有教育经历

GET /educations

> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": [
    {
      "id": 0,
      "userId": 0,
      "school": "string",
      "major": "string",
      "degree": 0,
      "startDate": "string",
      "endDate": "string",
      "visible": true,
      "gpa": "string"
    }
  ]
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## DELETE 删除教育经历

DELETE /educations/{id}

### 请求参数


| 名称  | 位置   | 类型      | 必选  | 说明   |
| --- | ---- | ------- | --- | ---- |
| id  | path | integer | 是   | none |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": null
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## GET 根据ID查询教育经历

GET /educations/{id}

### 请求参数


| 名称  | 位置   | 类型      | 必选  | 说明   |
| --- | ---- | ------- | --- | ---- |
| id  | path | integer | 是   | none |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": {
    "id": 0,
    "userId": 0,
    "school": "string",
    "major": "string",
    "degree": 0,
    "startDate": "string",
    "endDate": "string",
    "visible": true,
    "gpa": "string"
  }
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## 教育经历相关数据模型

- 请求模型：`EducationDTOCreateGroup`、`EducationDTOUpdateGroup`
- 响应模型：`Education`、`Result`

### 模型索引

- [EducationDTOCreateGroup](#schemaeducationdtocreategroup)
- [EducationDTOUpdateGroup](#schemaeducationdtoupdategroup)
- [Education](#schemaeducation)
- [Result](#schemaresult)
- [Result](#schemaresult)
- [Result](#schemaresult)

### 数据模型（内联）

#### EducationDTOCreateGroup（创建请求）


| 字段        | 类型      | 约束              |
| --------- | ------- | --------------- |
| school    | string  | 必填              |
| major     | string  | 必填              |
| degree    | integer | 必填，范围 1-6       |
| startDate | string  | 必填，`yyyy-MM-dd` |
| endDate   | string  | 可选，`yyyy-MM-dd` |
| visible   | boolean | 可选              |
| gpa       | string  | 可选              |


#### EducationDTOUpdateGroup（更新请求）


| 字段        | 类型             | 约束              |
| --------- | -------------- | --------------- |
| id        | integer(int64) | 必填              |
| school    | string         | 可选              |
| major     | string         | 可选              |
| degree    | integer        | 可选，若传则范围 1-6    |
| startDate | string         | 可选，`yyyy-MM-dd` |
| endDate   | string         | 可选，`yyyy-MM-dd` |
| visible   | boolean        | 可选              |
| gpa       | string         | 可选              |


## 教育经历异常场景


| 接口                        | 场景          | HTTP状态 | 业务码  |
| ------------------------- | ----------- | ------ | ---- |
| `POST /educations`        | 未登录         | 401    | 401  |
| `POST /educations`        | 添加失败        | 400    | 6001 |
| `PUT /educations`         | 教育经历 ID 为空  | 400    | 1001 |
| `PUT /educations`         | 毕业时间早于入学时间  | 400    | 6003 |
| `PUT /educations`         | 教育经历不存在     | 404    | 6006 |
| `PUT /educations`         | 无权限修改他人教育经历 | 403    | 1003 |
| `DELETE /educations/{id}` | 教育经历不存在     | 404    | 6006 |
| `DELETE /educations/{id}` | 无权限删除他人教育经历 | 403    | 1003 |


# 岗位信息控制器

## GET 查询岗位详情

GET /jobs/{id}

### 请求参数


| 名称  | 位置   | 类型      | 必选  | 说明   |
| --- | ---- | ------- | --- | ---- |
| id  | path | integer | 是   | 岗位ID |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": {
    "id": 0,
    "jobName": "string",
    "companyId": 0,
    "companyName": "string",
    "description": "string",
    "location": "string",
    "salaryMin": 0,
    "salaryMax": 0,
    "salaryType": 0,
    "salary": "string",
    "link": "string",
    "jobDuties": [
      "string"
    ],
    "jobRequirements": [
      "string"
    ],
    "keywords": [
      "string"
    ],
    "companyIndustries": [
      "string"
    ],
    "companySize": 0,
    "companyFundingType": 0
  }
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## DELETE 删除岗位

DELETE /jobs/{id}

### 请求参数


| 名称  | 位置   | 类型      | 必选  | 说明   |
| --- | ---- | ------- | --- | ---- |
| id  | path | integer | 是   | 岗位ID |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": null
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## GET 查询岗位列表（查询参数绑定[岗位分页查询条件（对应 GET /jobs 的查询参数）。]）

GET /jobs

示例：{@code GET /jobs?page=1&limit=20&employment=INTERN&filterSalaryMin=200&jobName=后端}

> Body 请求参数

```yaml
page: 0
limit: 0
companySizes:
  - 0
fundingTypes:
  - 0
industries:
  - string
companyName: string
employment: INTERN
filterSalaryMin: 0
filterSalaryMax: 0
jobName: string

```

### 请求参数


| 名称                | 位置   | 类型        | 必选  | 说明                         |
| ----------------- | ---- | --------- | --- | -------------------------- |
| body              | body | object    | 否   | none                       |
| » page            | body | integer   | 否   | 页码，默认 1；当`page<=0` 时按 1 处理 |
| » limit           | body | integer   | 否   | 每页条数，默认 20，最大 100（超出按 100） |
| » companySizes    | body | [integer] | 否   | 公司规模（多选）                   |
| » fundingTypes    | body | [integer] | 否   | 公司融资阶段（多选）                 |
| » industries      | body | [string]  | 否   | 行业（多选）                     |
| » companyName     | body | string    | 否   | 公司名称                       |
| » employment      | body | string    | 否   | 实习 / 全职；不传表示不限制            |
| » filterSalaryMin | body | integer   | 否   | 期望薪资下限（元）                  |
| » filterSalaryMax | body | integer   | 否   | 期望薪资上限（元）                  |
| » jobName         | body | string    | 否   | 岗位名称关键词（对 job_name 做包含匹配）  |


#### 详细说明

**» employment**: 实习 / 全职；不传表示不限制
INTERN :实习（日薪，salary_type = 1）
FULL_TIME :全职（月薪，salary_type = 2）

**分页边界处理**

- `page <= 0`：按 `1` 处理
- `limit <= 0`：按默认值 `20` 处理
- `limit > 100`：按上限 `100` 处理

#### 枚举值


| 属性           | 值         |
| ------------ | --------- |
| » employment | INTERN    |
| » employment | FULL_TIME |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": {
    "list": [
      {
        "id": 0,
        "jobName": "string",
        "companyId": 0,
        "companyName": "string",
        "description": "string",
        "location": "string",
        "salaryMin": 0,
        "salaryMax": 0,
        "salaryType": 0,
        "salary": "string",
        "link": "string",
        "jobDuties": [
          "string"
        ],
        "jobRequirements": [
          "string"
        ],
        "keywords": [
          "string"
        ],
        "companyIndustries": [
          "string"
        ],
        "companySize": 0,
        "companyFundingType": 0
      }
    ],
    "total": 0,
    "page": 0,
    "limit": 0,
    "totalPages": 0
  }
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## POST 创建岗位

POST /jobs

> Body 请求参数

```json
{
  "id": 0,
  "jobName": "string",
  "companyName": "string",
  "companySize": 0,
  "companyFundingType": 0,
  "companyIndustries": [
    "string"
  ],
  "companyIntroduction": "string",
  "description": "string",
  "location": "string",
  "salaryMin": 0,
  "salaryMax": 0,
  "salaryType": 0,
  "link": "string",
  "jobDuties": [
    "string"
  ],
  "jobRequirements": [
    "string"
  ],
  "keywords": [
    "string"
  ]
}
```

### 请求参数


| 名称   | 位置   | 类型                                            | 必选  | 说明   |
| ---- | ---- | --------------------------------------------- | --- | ---- |
| body | body | [JobDTOCreateGroup](#schemajobdtocreategroup) | 否   | none |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": {
    "id": 0,
    "jobName": "string",
    "companyId": 0,
    "companyName": "string",
    "description": "string",
    "location": "string",
    "salaryMin": 0,
    "salaryMax": 0,
    "salaryType": 0,
    "content": [
      "string"
    ],
    "requirements": [
      "string"
    ],
    "keywords": [
      "string"
    ],
    "link": "string"
  }
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## PUT 更新岗位

PUT /jobs

> Body 请求参数

```json
{
  "id": 0,
  "jobName": "string",
  "companyName": "string",
  "companySize": 0,
  "companyFundingType": 0,
  "companyIndustries": [
    "string"
  ],
  "companyIntroduction": "string",
  "description": "string",
  "location": "string",
  "salaryMin": 0,
  "salaryMax": 0,
  "salaryType": 0,
  "link": "string",
  "jobDuties": [
    "string"
  ],
  "jobRequirements": [
    "string"
  ],
  "keywords": [
    "string"
  ]
}
```

### 请求参数


| 名称   | 位置   | 类型                                            | 必选  | 说明   |
| ---- | ---- | --------------------------------------------- | --- | ---- |
| body | body | [JobDTOUpdateGroup](#schemajobdtoupdategroup) | 否   | none |


> 返回示例

> 200 Response

```json
{
  "code": 0,
  "msg": "string",
  "data": {
    "id": 0,
    "jobName": "string",
    "companyId": 0,
    "companyName": "string",
    "description": "string",
    "location": "string",
    "salaryMin": 0,
    "salaryMax": 0,
    "salaryType": 0,
    "content": [
      "string"
    ],
    "requirements": [
      "string"
    ],
    "keywords": [
      "string"
    ],
    "link": "string"
  }
}
```

### 返回结果


| 状态码 | 状态码含义                                                   | 说明   | 数据模型                    |
| --- | ------------------------------------------------------- | ---- | ----------------------- |
| 200 | [OK](https://tools.ietf.org/html/rfc7231#section-6.3.1) | none | [Result](#schemaresult) |


## 岗位相关数据模型

- 请求模型：`JobDTOCreateGroup`、`JobDTOUpdateGroup`
- 响应模型：`Job`、`JobListItemDTO`、`PageDTOJobListItemDTO`、`Result`

### 模型索引

- [JobDTOCreateGroup](#schemajobdtocreategroup)
- [JobDTOUpdateGroup](#schemajobdtoupdategroup)
- [Job](#schemajob)
- [JobListItemDTO](#schemajoblistitemdto)
- [PageDTOJobListItemDTO](#schemapagedtojoblistitemdto)
- [Result](#schemaresult)
- [Result](#schemaresult)
- [Result](#schemaresult)
- [Result](#schemaresult)

### 数据模型（内联）

#### JobDTOCreateGroup / JobDTOUpdateGroup（创建/更新请求）


| 字段                  | 类型             | 约束                  |
| ------------------- | -------------- | ------------------- |
| id                  | integer(int64) | 更新时必填               |
| jobName             | string         | 创建/更新必填             |
| companyName         | string         | 创建/更新必填             |
| description         | string         | 创建/更新必填             |
| location            | string         | 创建/更新必填             |
| salaryMin           | integer        | 创建/更新必填             |
| salaryMax           | integer        | 创建/更新必填             |
| salaryType          | integer        | 创建/更新必填，1日薪/2月薪/3年薪 |
| link                | string         | 创建/更新必填             |
| companySize         | integer        | 可选                  |
| companyFundingType  | integer        | 可选                  |
| companyIndustries   | array[string]  | 可选                  |
| companyIntroduction | string         | 可选                  |
| jobDuties           | array[string]  | 可选                  |
| jobRequirements     | array[string]  | 可选                  |
| keywords            | array[string]  | 可选                  |


#### JobListItemDTO（详情/列表响应 data）


| 字段                 | 类型             | 说明          |
| ------------------ | -------------- | ----------- |
| id                 | integer(int64) | 岗位ID        |
| jobName            | string         | 岗位名称        |
| companyId          | integer(int64) | 公司ID        |
| companyName        | string         | 公司名称        |
| description        | string         | 岗位描述        |
| location           | string         | 工作地点        |
| salaryMin          | integer        | 最低薪资        |
| salaryMax          | integer        | 最高薪资        |
| salaryType         | integer        | 1日薪/2月薪/3年薪 |
| salary             | string         | 薪资展示文案      |
| link               | string         | 岗位链接        |
| jobDuties          | array[string]  | 岗位职责        |
| jobRequirements    | array[string]  | 岗位要求        |
| keywords           | array[string]  | 岗位关键词       |
| companyIndustries  | array[string]  | 公司所属行业      |
| companySize        | integer        | 公司规模        |
| companyFundingType | integer        | 融资阶段        |


## 岗位异常场景


| 接口                  | 场景                       | HTTP状态 | 业务码  |
| ------------------- | ------------------------ | ------ | ---- |
| `GET /jobs/{id}`    | 岗位不存在                    | 404    | 8201 |
| `PUT /jobs`         | 岗位不存在                    | 404    | 8201 |
| `DELETE /jobs/{id}` | 岗位不存在                    | 404    | 8201 |
| `POST /jobs`        | 参数校验失败（必填字段缺失）           | 400    | 1001 |
| `GET /jobs`         | `page <= 0` 自动纠正为 `1`    | 200    | -    |
| `GET /jobs`         | `limit <= 0` 自动纠正为 `20`  | 200    | -    |
| `GET /jobs`         | `limit > 100` 按 `100` 截断 | 200    | -    |


# 数据模型定义

> 已在各接口模块内提供对应的数据模型索引，请通过上面的“模型索引”快速定位。



## Education



```json
{
  "id": 0,
  "userId": 0,
  "school": "string",
  "major": "string",
  "degree": 0,
  "startDate": "string",
  "endDate": "string",
  "visible": true,
  "gpa": "string"
}

```

### 属性


| 名称        | 类型             | 必选    | 约束   | 说明            |     |
| --------- | -------------- | ----- | ---- | ------------- | --- |
| id        | integer(int64) | false | none | 教育经历ID（主键，自增） |     |
| userId    | integer(int64) | false | none | 用户ID          |     |
| school    | string         | false | none | 学校名称          |     |
| major     | string         | false | none | 专业名称          |     |
| degree    | integer        | false | none | 学历层次          |     |
| startDate | string         | false | none | 入学时间          |     |
| endDate   | string         | false | none | 毕业时间          |     |
| visible   | boolean        | false | none | 是否在简历中展示      |     |
| gpa       | string         | false | none | 绩点            |     |




## UserDTO






```json
{
  "id": 0,
  "username": "string",
  "email": "string",
  "name": "string",
  "phone": "string",
  "website": "string"
}

```

### 属性


| 名称       | 类型             | 必选    | 约束   | 说明   |     |
| -------- | -------------- | ----- | ---- | ---- | --- |
| id       | integer(int64) | false | none | 用户ID |     |
| username | string         | false | none | 用户名  |     |
| email    | string         | false | none | 邮箱   |     |
| name     | string         | false | none | 用户姓名 |     |
| phone    | string         | false | none | 电话   |     |
| website  | string         | false | none | 主页链接 |     |




## EducationDTOCreateGroup






```json
{
  "id": 0,
  "school": "string",
  "major": "string",
  "degree": 1,
  "startDate": "string",
  "endDate": "string",
  "visible": true,
  "gpa": "string"
}

```

### 属性


| 名称        | 类型             | 必选    | 约束   | 说明                                                                          |     |
| --------- | -------------- | ----- | ---- | --------------------------------------------------------------------------- | --- |
| id        | integer(int64) | false | none | 教育经历ID 更新时必填，创建时不需要                                                         |     |
| school    | string         | true  | none | 学校名称 创建时必填，更新时可选                                                            |     |
| major     | string         | true  | none | 专业名称 创建时必填，更新时可选                                                            |     |
| degree    | integer        | true  | none | 学历层次（1: 博士, 2: 硕士, 3: 本科, 4: 大专, 5: 高中, 6: 其他） 创建时必填，更新时可选（若提供，取值范围必须为 1-6） |     |
| startDate | string         | true  | none | 入学时间（格式：YYYY-MM-DD，例如：2020-09-01） 创建时必填，更新时可选                               |     |
| endDate   | string         | false | none | 毕业时间（格式：YYYY-MM-DD，例如：2024-06-30） 创建和更新时都可选（在读情况）                           |     |
| visible   | boolean        | false | none | 是否在简历中展示                                                                    |     |
| gpa       | string         | false | none | 绩点                                                                          |     |




## JobListItemDTO






```json
{
  "id": 0,
  "jobName": "string",
  "companyId": 0,
  "companyName": "string",
  "description": "string",
  "location": "string",
  "salaryMin": 0,
  "salaryMax": 0,
  "salaryType": 0,
  "salary": "string",
  "link": "string",
  "jobDuties": [
    "string"
  ],
  "jobRequirements": [
    "string"
  ],
  "keywords": [
    "string"
  ],
  "companyIndustries": [
    "string"
  ],
  "companySize": 0,
  "companyFundingType": 0
}

```

### 属性


| 名称                 | 类型             | 必选    | 约束   | 说明                         |     |
| ------------------ | -------------- | ----- | ---- | -------------------------- | --- |
| id                 | integer(int64) | false | none | 岗位ID                       |     |
| jobName            | string         | false | none | 岗位名称                       |     |
| companyId          | integer(int64) | false | none | 公司ID                       |     |
| companyName        | string         | false | none | 公司名称                       |     |
| description        | string         | false | none | 岗位描述                       |     |
| location           | string         | false | none | 工作地点                       |     |
| salaryMin          | integer        | false | none | 最低薪资（元）                    |     |
| salaryMax          | integer        | false | none | 最高薪资（元）                    |     |
| salaryType         | integer        | false | none | 薪资类型：1=日薪 / 2=月薪 / 3=年薪    |     |
| salary             | string         | false | none | 薪资展示文案                     |     |
| link               | string         | false | none | 岗位链接                       |     |
| jobDuties          | [string]       | false | none | 岗位职责（对应 jobs.content）      |     |
| jobRequirements    | [string]       | false | none | 岗位要求（对应 jobs.requirements） |     |
| keywords           | [string]       | false | none | 岗位关键词（对应 jobs.keywords）    |     |
| companyIndustries  | [string]       | false | none | 公司所属行业                     |     |
| companySize        | integer        | false | none | 公司人员规模                     |     |
| companyFundingType | integer        | false | none | 公司融资阶段                     |     |




## EducationDTOUpdateGroup






```json
{
  "id": 0,
  "school": "string",
  "major": "string",
  "degree": 1,
  "startDate": "string",
  "endDate": "string",
  "visible": true,
  "gpa": "string"
}

```

### 属性


| 名称        | 类型             | 必选    | 约束   | 说明                                                                          |     |
| --------- | -------------- | ----- | ---- | --------------------------------------------------------------------------- | --- |
| id        | integer(int64) | true  | none | 教育经历ID 更新时必填，创建时不需要                                                         |     |
| school    | string         | false | none | 学校名称 创建时必填，更新时可选                                                            |     |
| major     | string         | false | none | 专业名称 创建时必填，更新时可选                                                            |     |
| degree    | integer        | false | none | 学历层次（1: 博士, 2: 硕士, 3: 本科, 4: 大专, 5: 高中, 6: 其他） 创建时必填，更新时可选（若提供，取值范围必须为 1-6） |     |
| startDate | string         | false | none | 入学时间（格式：YYYY-MM-DD，例如：2020-09-01） 创建时必填，更新时可选                               |     |
| endDate   | string         | false | none | 毕业时间（格式：YYYY-MM-DD，例如：2024-06-30） 创建和更新时都可选（在读情况）                           |     |
| visible   | boolean        | false | none | 是否在简历中展示                                                                    |     |
| gpa       | string         | false | none | 绩点                                                                          |     |




## PageDTOJobListItemDTO






```json
{
  "list": [
    {
      "id": 0,
      "jobName": "string",
      "companyId": 0,
      "companyName": "string",
      "description": "string",
      "location": "string",
      "salaryMin": 0,
      "salaryMax": 0,
      "salaryType": 0,
      "salary": "string",
      "link": "string",
      "jobDuties": [
        "string"
      ],
      "jobRequirements": [
        "string"
      ],
      "keywords": [
        "string"
      ],
      "companyIndustries": [
        "string"
      ],
      "companySize": 0,
      "companyFundingType": 0
    }
  ],
  "total": 0,
  "page": 0,
  "limit": 0,
  "totalPages": 0
}

```

### 属性


| 名称         | 类型                                        | 必选    | 约束   | 说明                                       |     |
| ---------- | ----------------------------------------- | ----- | ---- | ---------------------------------------- | --- |
| list       | [[JobListItemDTO](#schemajoblistitemdto)] | false | none | 当前页数据                                    |     |
| total      | integer(int64)                            | false | none | 总条数                                      |     |
| page       | integer                                   | false | none | 当前页码（从 1 开始，与常见 query 参数{@code page} 一致） |     |
| limit      | integer                                   | false | none | 每页条数                                     |     |
| totalPages | integer                                   | false | none | 总页数                                      |     |




## UserRegisterDTO






```json
{
  "username": "string",
  "password": "string",
  "email": "user@example.com",
  "verificationCode": "string"
}

```

### 属性


| 名称               | 类型            | 必选   | 约束   | 说明                            |     |
| ---------------- | ------------- | ---- | ---- | ----------------------------- | --- |
| username         | string        | true | none | 用户名（不能为空，长度 3-50 个字符）         |     |
| password         | string        | true | none | 密码（不能为空，长度 6-20 个字符）          |     |
| email            | string(email) | true | none | 邮箱（不能为空，需符合邮箱格式，最大长度 100 个字符） |     |
| verificationCode | string        | true | none | 验证码（不能为空，固定 6 位）              |     |




## UserLoginResponseDTO






```json
{
  "token": "string",
  "user": {
    "id": 0,
    "username": "string",
    "email": "string",
    "name": "string",
    "phone": "string",
    "website": "string"
  }
}

```

### 属性


| 名称    | 类型                        | 必选    | 约束   | 说明        |     |
| ----- | ------------------------- | ----- | ---- | --------- | --- |
| token | string                    | false | none | JWT Token |     |
| user  | [UserDTO](#schemauserdto) | false | none | 用户信息      |     |




## UserLoginDTO






```json
{
  "usernameOrEmail": "string",
  "password": "string"
}

```

### 属性


| 名称              | 类型     | 必选   | 约束   | 说明           |     |
| --------------- | ------ | ---- | ---- | ------------ | --- |
| usernameOrEmail | string | true | none | 用户名或邮箱（不能为空） |     |
| password        | string | true | none | 密码（不能为空）     |     |




## Job






```json
{
  "id": 0,
  "jobName": "string",
  "companyId": 0,
  "companyName": "string",
  "description": "string",
  "location": "string",
  "salaryMin": 0,
  "salaryMax": 0,
  "salaryType": 0,
  "content": [
    "string"
  ],
  "requirements": [
    "string"
  ],
  "keywords": [
    "string"
  ],
  "link": "string"
}

```

### 属性


| 名称           | 类型             | 必选    | 约束   | 说明                                        |     |
| ------------ | -------------- | ----- | ---- | ----------------------------------------- | --- |
| id           | integer(int64) | false | none | 岗位ID                                      |     |
| jobName      | string         | false | none | 岗位名称                                      |     |
| companyId    | integer(int64) | false | none | 公司ID                                      |     |
| companyName  | string         | false | none | 公司名称                                      |     |
| description  | string         | false | none | 岗位描述                                      |     |
| location     | string         | false | none | 工作地点                                      |     |
| salaryMin    | integer        | false | none | 最低薪资（元），与 salary_max、salary_type 共同表示薪资范围 |     |
| salaryMax    | integer        | false | none | 最高薪资（元）                                   |     |
| salaryType   | integer        | false | none | 薪资类型：1=日薪 / 2=月薪 / 3=年薪                   |     |
| content      | [string]       | false | none | 岗位职责（JSONB 字符串数组）                         |     |
| requirements | [string]       | false | none | 岗位要求（JSONB 字符串数组）                         |     |
| keywords     | [string]       | false | none | 岗位关键词（JSONB 字符串数组）                        |     |
| link         | string         | false | none | 岗位链接                                      |     |




## UserResetPasswordDTO






```json
{
  "email": "user@example.com",
  "verificationCode": "string",
  "newPassword": "string"
}

```

### 属性


| 名称               | 类型            | 必选   | 约束   | 说明                    |     |
| ---------------- | ------------- | ---- | ---- | --------------------- | --- |
| email            | string(email) | true | none | 邮箱地址（不能为空，需符合邮箱格式）    |     |
| verificationCode | string        | true | none | 验证码（不能为空，固定 6 位）      |     |
| newPassword      | string        | true | none | 新密码（不能为空，长度 6-50 个字符） |     |




## JobDTOCreateGroup






```json
{
  "id": 0,
  "jobName": "string",
  "companyName": "string",
  "companySize": 0,
  "companyFundingType": 0,
  "companyIndustries": [
    "string"
  ],
  "companyIntroduction": "string",
  "description": "string",
  "location": "string",
  "salaryMin": 0,
  "salaryMax": 0,
  "salaryType": 0,
  "link": "string",
  "jobDuties": [
    "string"
  ],
  "jobRequirements": [
    "string"
  ],
  "keywords": [
    "string"
  ]
}

```

### 属性


| 名称                  | 类型             | 必选    | 约束   | 说明                                  |     |
| ------------------- | -------------- | ----- | ---- | ----------------------------------- | --- |
| id                  | integer(int64) | false | none | 岗位ID（更新时不能为空）                       |     |
| jobName             | string         | true  | none | 岗位名称（创建、更新时不能为空）                    |     |
| companyName         | string         | true  | none | 公司名称（创建、更新时不能为空）                    |     |
| companySize         | integer        | false | none | 公司人员规模（可选），未传时为空                    |     |
| companyFundingType  | integer        | false | none | 公司融资阶段（可选）                          |     |
| companyIndustries   | [string]       | false | none | 公司所属行业（可选）                          |     |
| companyIntroduction | string         | false | none | 公司介绍（可选）                            |     |
| description         | string         | true  | none | 岗位描述（创建、更新时不能为空）                    |     |
| location            | string         | true  | none | 工作地点（创建、更新时不能为空）                    |     |
| salaryMin           | integer        | true  | none | 最低薪资（创建、更新时不能为空）                    |     |
| salaryMax           | integer        | true  | none | 最高薪资（创建、更新时不能为空）                    |     |
| salaryType          | integer        | true  | none | 薪资类型（创建、更新时不能为空）：1=日薪 / 2=月薪 / 3=年薪 |     |
| link                | string         | true  | none | 岗位链接（创建、更新时不能为空）                    |     |
| jobDuties           | [string]       | false | none | 岗位职责（字符串列表，写入 jobs.content）         |     |
| jobRequirements     | [string]       | false | none | 岗位要求（字符串列表，写入 jobs.requirements）    |     |
| keywords            | [string]       | false | none | 岗位关键词（字符串列表，写入 jobs.keywords）       |     |




## UserUpdateDTO






```json
{
  "name": "string",
  "phone": "string",
  "website": "string"
}

```

### 属性


| 名称      | 类型     | 必选    | 约束   | 说明                 |     |
| ------- | ------ | ----- | ---- | ------------------ | --- |
| name    | string | false | none | 用户姓名（最大长度 100 个字符） |     |
| phone   | string | false | none | 电话（最大长度 50 个字符）    |     |
| website | string | false | none | 主页链接（最大长度 255 个字符） |     |




## JobDTOUpdateGroup






```json
{
  "id": 0,
  "jobName": "string",
  "companyName": "string",
  "companySize": 0,
  "companyFundingType": 0,
  "companyIndustries": [
    "string"
  ],
  "companyIntroduction": "string",
  "description": "string",
  "location": "string",
  "salaryMin": 0,
  "salaryMax": 0,
  "salaryType": 0,
  "link": "string",
  "jobDuties": [
    "string"
  ],
  "jobRequirements": [
    "string"
  ],
  "keywords": [
    "string"
  ]
}

```

### 属性


| 名称                  | 类型             | 必选    | 约束   | 说明                                  |     |
| ------------------- | -------------- | ----- | ---- | ----------------------------------- | --- |
| id                  | integer(int64) | true  | none | 岗位ID（更新时不能为空）                       |     |
| jobName             | string         | true  | none | 岗位名称（创建、更新时不能为空）                    |     |
| companyName         | string         | true  | none | 公司名称（创建、更新时不能为空）                    |     |
| companySize         | integer        | false | none | 公司人员规模（可选），未传时为空                    |     |
| companyFundingType  | integer        | false | none | 公司融资阶段（可选）                          |     |
| companyIndustries   | [string]       | false | none | 公司所属行业（可选）                          |     |
| companyIntroduction | string         | false | none | 公司介绍（可选）                            |     |
| description         | string         | true  | none | 岗位描述（创建、更新时不能为空）                    |     |
| location            | string         | true  | none | 工作地点（创建、更新时不能为空）                    |     |
| salaryMin           | integer        | true  | none | 最低薪资（创建、更新时不能为空）                    |     |
| salaryMax           | integer        | true  | none | 最高薪资（创建、更新时不能为空）                    |     |
| salaryType          | integer        | true  | none | 薪资类型（创建、更新时不能为空）：1=日薪 / 2=月薪 / 3=年薪 |     |
| link                | string         | true  | none | 岗位链接（创建、更新时不能为空）                    |     |
| jobDuties           | [string]       | false | none | 岗位职责（字符串列表，写入 jobs.content）         |     |
| jobRequirements     | [string]       | false | none | 岗位要求（字符串列表，写入 jobs.requirements）    |     |
| keywords            | [string]       | false | none | 岗位关键词（字符串列表，写入 jobs.keywords）       |     |




## Result






```json
{
  "code": 0,
  "msg": "string",
  "data": {}
}

```

### 属性


| 名称   | 类型      | 必选    | 约束   | 说明             |
| ---- | ------- | ----- | ---- | -------------- |
| code | integer | false | none | 业务码（非HTTP状态码）  |
| msg  | string  | false | none | 消息提示           |
| data | object  | false | none | 返回数据（不同接口类型不同） |


