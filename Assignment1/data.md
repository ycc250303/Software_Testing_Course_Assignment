# 基于 LLM 的 REST API 文档黑盒测试分析报告

LLM-Based Black-Box Testing for RESTful API Documentation

---

## 一、项目概述

本次作业采用黑盒动态测试方法，以项目后端 Java 模块的 8 个 REST API 接口为被测对象，使用大语言模型（LLM）作为测试辅助工具，自动生成基于等价类划分（EP）和边界值分析（BVA）的测试用例，并对生成结果进行覆盖率、准确率及缺失项分析。

**被测接口列表：**


| 编号  | 接口                  | 方法   | 功能         |
| --- | ------------------- | ---- | ---------- |
| 1   | POST /users         | POST | 用户注册       |
| 2   | GET /users/{id}     | GET  | 根据 ID 查询用户 |
| 3   | PUT /users/password | PUT  | 重置密码       |
| 4   | POST /educations    | POST | 添加教育经历     |
| 5   | PUT /educations     | PUT  | 更新教育经历     |
| 6   | GET /jobs           | GET  | 查询岗位列表（分页） |
| 7   | POST /jobs          | POST | 创建岗位       |
| 8   | PUT /jobs           | PUT  | 更新岗位       |


---

## 二、使用的工具与 Prompt

**使用模型：** GPT-5.3

**使用的 Prompt（示例）：**

```
You are a software testing assistant. Given the following REST API interface document,
please generate black-box test cases using Equivalence Partitioning (EP) and
Boundary Value Analysis (BVA).

For each parameter, identify:
1. Valid equivalence classes
2. Invalid equivalence classes
3. Boundary values

Then generate concrete test cases in a table with columns:
Test Case ID | Precondition | Request Body / Params | Expected HTTP Status | Expected Business Code | Test Type

Interface document:
{interface_document}
```

---

## 三、等价类划分与边界值分析（手动整理的理论全集）

以下为根据接口文档手动整理的全部等价类（EP）和边界值（BVA），作为覆盖率计算的基准。

### 3.1 POST /users（用户注册）

**等价类划分：**


| EP 编号 | 参数               | 描述              | 类型  |
| ----- | ---------------- | --------------- | --- |
| EP1   | username         | 长度 3–50 个字符     | 有效  |
| EP2   | username         | 长度 < 3（如1–2位）   | 无效  |
| EP3   | username         | 长度 > 50         | 无效  |
| EP4   | username         | 为空/null         | 无效  |
| EP5   | password         | 长度 6–20 个字符     | 有效  |
| EP6   | password         | 长度 < 6          | 无效  |
| EP7   | password         | 长度 > 20         | 无效  |
| EP8   | password         | 为空/null         | 无效  |
| EP9   | email            | 符合邮箱格式，长度 ≤ 100 | 有效  |
| EP10  | email            | 不符合邮箱格式         | 无效  |
| EP11  | email            | 长度 > 100        | 无效  |
| EP12  | email            | 为空/null         | 无效  |
| EP13  | verificationCode | 恰好 6 位字符串       | 有效  |
| EP14  | verificationCode | 长度 ≠ 6（如 5位、7位） | 无效  |
| EP15  | verificationCode | 为空/null         | 无效  |
| EP16  | username         | 用户名已存在 → 409    | 无效  |
| EP17  | email            | 邮箱已存在 → 409     | 无效  |


**边界值分析（共 10 个 BV）：**


| 参数               | 边界测试值                         |
| ---------------- | ----------------------------- |
| username         | 长度 2, 3, 4（下界）；49, 50, 51（上界） |
| password         | 长度 5, 6, 7（下界）；19, 20, 21（上界） |
| email            | 长度 99, 100, 101（上界）           |
| verificationCode | 长度 5, 6, 7                    |


---

### 3.2 GET /users/{id}（根据 ID 查用户）

**等价类划分：**


| EP 编号 | 参数   | 描述                       | 类型  |
| ----- | ---- | ------------------------ | --- |
| EP1   | id   | 存在的正整数 ID → 200          | 有效  |
| EP2   | id   | 不存在的正整数 ID → 404         | 无效  |
| EP3   | id   | 0 或负数 → 400/404          | 无效  |
| EP4   | id   | 非整数（如字符串）→ 400           | 无效  |
| EP5   | Auth | 无 Token / Token 无效 → 401 | 无效  |
| EP6   | Auth | Token 有效但越权查他人 → 403     | 无效  |


**边界值分析（共 4 个 BV）：**


| 参数  | 边界测试值                       |
| --- | --------------------------- |
| id  | 0, 1, 2（下界）；最大整数值，最大值+1（上界） |


---

### 3.3 PUT /users/password（重置密码）

**等价类划分：**


| EP 编号 | 参数               | 描述                 | 类型  |
| ----- | ---------------- | ------------------ | --- |
| EP1   | email            | 已注册的有效邮箱格式         | 有效  |
| EP2   | email            | 未注册的邮箱 → 404       | 无效  |
| EP3   | email            | 不符合邮箱格式 → 400      | 无效  |
| EP4   | email            | 为空/null → 400      | 无效  |
| EP5   | verificationCode | 恰好 6 位且正确          | 有效  |
| EP6   | verificationCode | 6 位但错误 → 400(2003) | 无效  |
| EP7   | verificationCode | 长度 ≠ 6 → 400       | 无效  |
| EP8   | verificationCode | 为空/null → 400      | 无效  |
| EP9   | newPassword      | 长度 6–50 个字符        | 有效  |
| EP10  | newPassword      | 长度 < 6 → 400       | 无效  |
| EP11  | newPassword      | 长度 > 50 → 400      | 无效  |
| EP12  | newPassword      | 为空/null → 400      | 无效  |


**边界值分析（共 8 个 BV）：**


| 参数               | 边界测试值                         |
| ---------------- | ----------------------------- |
| verificationCode | 长度 5, 6, 7                    |
| newPassword      | 长度 5, 6, 7（下界）；49, 50, 51（上界） |


---

### 3.4 POST /educations（添加教育经历）

**等价类划分：**


| EP 编号 | 参数        | 描述                          | 类型  |
| ----- | --------- | --------------------------- | --- |
| EP1   | school    | 非空字符串                       | 有效  |
| EP2   | school    | 为空/null → 400               | 无效  |
| EP3   | major     | 非空字符串                       | 有效  |
| EP4   | major     | 为空/null → 400               | 无效  |
| EP5   | degree    | 1–6 之间整数                    | 有效  |
| EP6   | degree    | < 1 或 > 6 → 400             | 无效  |
| EP7   | degree    | 为空/null → 400               | 无效  |
| EP8   | startDate | 格式 YYYY-MM-DD 合法日期          | 有效  |
| EP9   | startDate | 格式错误 → 400                  | 无效  |
| EP10  | startDate | 为空/null → 400               | 无效  |
| EP11  | endDate   | 合法 YYYY-MM-DD 且晚于 startDate | 有效  |
| EP12  | endDate   | 早于 startDate → 400          | 无效  |
| EP13  | endDate   | 不传（在读） → 200                | 有效  |
| EP14  | visible   | true 或 false                | 有效  |
| EP15  | Auth      | 无 Token / 无效 → 401          | 无效  |


**边界值分析（共 6 个 BV）：**


| 参数        | 边界测试值                   |
| --------- | ----------------------- |
| degree    | 0, 1, 2（下界）；5, 6, 7（上界） |
| startDate | 合法最小日期；超出范围格式           |


---

### 3.5 PUT /educations（更新教育经历）

**等价类划分：**


| EP 编号 | 参数     | 描述                   | 类型  |
| ----- | ------ | -------------------- | --- |
| EP1   | id     | 存在且属于当前用户的记录 ID      | 有效  |
| EP2   | id     | 不存在的 ID → 404        | 无效  |
| EP3   | id     | 为空/null（必填）→ 400     | 无效  |
| EP4   | id     | 属于其他用户的 ID → 403     | 无效  |
| EP5   | degree | 1–6（若提供）             | 有效  |
| EP6   | degree | < 1 或 > 6（若提供）→ 400  | 无效  |
| EP7   | 其他字段   | 只更新部分字段（其余不传）→ 200   | 有效  |
| EP8   | 其他字段   | 仅传 id，所有可选字段不传 → 200 | 有效  |


**边界值分析（共 4 个 BV）：**


| 参数     | 边界测试值                   |
| ------ | ----------------------- |
| degree | 0, 1, 2（下界）；5, 6, 7（上界） |


---

### 3.6 GET /jobs（查询岗位列表）

**等价类划分：**


| EP 编号 | 参数                  | 描述                     | 类型      |
| ----- | ------------------- | ---------------------- | ------- |
| EP1   | page                | 正整数（正常分页）              | 有效      |
| EP2   | page                | ≤ 0（自动纠正为 1）→ 200      | 有效（软边界） |
| EP3   | page                | 不传（默认 1）→ 200          | 有效      |
| EP4   | limit               | 1–100（含边界）             | 有效      |
| EP5   | limit               | ≤ 0（自动纠正为 20）→ 200     | 有效（软边界） |
| EP6   | limit               | > 100（截断为 100）→ 200    | 有效（软边界） |
| EP7   | limit               | 不传（默认 20）→ 200         | 有效      |
| EP8   | employment          | "INTERN" 或 "FULL_TIME" | 有效      |
| EP9   | employment          | 非枚举值 → 400             | 无效      |
| EP10  | employment          | 不传（不限制）→ 200           | 有效      |
| EP11  | filterSalaryMin/Max | min ≤ max 的合理区间        | 有效      |
| EP12  | filterSalaryMin/Max | min > max（逻辑错误）        | 无效      |
| EP13  | jobName             | 有效关键词（包含匹配）            | 有效      |
| EP14  | jobName             | 无匹配结果 → 200（空列表）       | 有效      |


**边界值分析（共 6 个 BV）：**


| 参数    | 边界测试值                          |
| ----- | ------------------------------ |
| page  | -1, 0, 1（下界自动纠正）               |
| limit | 0, 1, 2（下界）；99, 100, 101（上界截断） |


---

### 3.7 POST /jobs（创建岗位）

**等价类划分：**


| EP 编号 | 参数                        | 描述                          | 类型  |
| ----- | ------------------------- | --------------------------- | --- |
| EP1   | jobName                   | 非空字符串（必填）                   | 有效  |
| EP2   | jobName                   | 为空/null → 400(1001)         | 无效  |
| EP3   | companyName               | 非空字符串（必填）                   | 有效  |
| EP4   | companyName               | 为空/null → 400               | 无效  |
| EP5   | salaryType                | 1、2 或 3（枚举值）                | 有效  |
| EP6   | salaryType                | 枚举范围之外（如 0、4）→ 400          | 无效  |
| EP7   | salaryType                | 为空/null → 400               | 无效  |
| EP8   | salaryMin/Max             | salaryMin ≤ salaryMax       | 有效  |
| EP9   | salaryMin/Max             | salaryMin > salaryMax → 400 | 无效  |
| EP10  | salaryMin/Max             | 为空/null → 400               | 无效  |
| EP11  | description/location/link | 非空字符串（必填）                   | 有效  |
| EP12  | description/location/link | 任一为空/null → 400             | 无效  |
| EP13  | 可选字段                      | companySize 等均不传 → 200      | 有效  |
| EP14  | Auth                      | 无 Token / 无效 → 401          | 无效  |


**边界值分析（共 4 个 BV）：**


| 参数         | 边界测试值                |
| ---------- | -------------------- |
| salaryType | 0, 1, 2（下界）；3, 4（上界） |
| salaryMin  | 0, 1（最低合理值下界）；负数     |


---

### 3.8 PUT /jobs（更新岗位）

**等价类划分（同 POST /jobs，增加 id 相关）：**


| EP 编号 | 参数                        | 描述                  | 类型  |
| ----- | ------------------------- | ------------------- | --- |
| EP1   | id                        | 存在的岗位 ID            | 有效  |
| EP2   | id                        | 不存在的 ID → 404(8201) | 无效  |
| EP3   | id                        | 为空/null → 400       | 无效  |
| EP4   | id                        | 非整数类型 → 400         | 无效  |
| EP5   | salaryType                | 1、2 或 3             | 有效  |
| EP6   | salaryType                | 枚举范围外 → 400         | 无效  |
| EP7   | jobName/companyName 等必填字段 | 非空字符串               | 有效  |
| EP8   | jobName/companyName 等必填字段 | 为空/null → 400       | 无效  |
| EP9   | Auth                      | 无 Token / 无效 → 401  | 无效  |


**边界值分析（共 2 个 BV）：**


| 参数         | 边界测试值                |
| ---------- | -------------------- |
| salaryType | 0, 1, 2（下界）；3, 4（上界） |


---

## 四、AI 生成的测试用例

以下为 LLM 针对 8 个接口生成的全部测试用例（共 83 条）。

### 接口 1：POST /users（12 条）


| 用例ID  | 请求Body摘要                      | 预期HTTP | 预期业务码 | 测试类型           |
| ----- | ----------------------------- | ------ | ----- | -------------- |
| TC01  | 全部合法参数（username=abc，3位）       | 200    | 0     | 正常注册           |
| TC02  | 缺少 username                   | 400    | 1001  | 缺少必填字段         |
| TC03  | username=ab（2位，太短）            | 400    | 1001  | username 边界-太短 |
| TC04  | username 51 字符（太长）            | 400    | 1001  | username 边界-太长 |
| TC05  | password=12345（5位，太短）         | 400    | 1001  | password 边界-太短 |
| TC06  | password=21位（太长）              | 400    | 1001  | password 边界-太长 |
| TC07  | email="invalid-email"（格式无效）   | 400    | 1001  | email 格式无效     |
| TC08  | email=101字符（超长）               | 400    | 1001  | email 超长       |
| TC09  | verificationCode=12345（5位，不足） | 400    | 1001  | 验证码不足6位        |
| TC10a | username 已存在                  | 409    | 2001  | 用户名重复          |
| TC10b | email 已存在                     | 409    | 2002  | 邮箱重复           |
| TC10c | 验证码格式正确但内容无效                  | 400    | 2003  | 验证码无效          |


### 接口 2：GET /users/{id}（10 条）


| 用例ID | 请求参数                     | 预期HTTP  | 预期业务码  | 测试类型     |
| ---- | ------------------------ | ------- | ------ | -------- |
| TC01 | id=1（存在）                 | 200     | 0      | 正常查询     |
| TC02 | id=99999（不存在）            | 404     | 2006   | 用户不存在    |
| TC03 | id=abc（非数字）              | 400     | 1001   | id 非数字   |
| TC04 | id=1.5（小数）               | 400     | 1001   | id 小数    |
| TC05 | id=-1（负数）                | 400     | 1001   | id 负数    |
| TC06 | id=0                     | 400     | 1001   | id=0     |
| TC07 | id=2147483647（最大值）       | 200/404 | 0/2006 | id 大值    |
| TC08 | id=2147483648（超出 int 范围） | 400     | 1001   | id 超出范围  |
| TC09 | 无 Token                  | 401     | 401    | 未认证      |
| TC10 | 无效 Token                 | 401     | 401    | Token 无效 |


### 接口 3：PUT /users/password（10 条）


| 用例ID | 请求Body摘要                   | 预期HTTP | 预期业务码 | 测试类型       |
| ---- | -------------------------- | ------ | ----- | ---------- |
| TC01 | 全部合法（newPassword=6位）       | 200    | 0     | 正常重置       |
| TC02 | newPassword=50位（最大边界）      | 200    | 0     | 新密码最大值     |
| TC03 | 缺少 email                   | 400    | 1001  | 缺少必填字段     |
| TC04 | email="invalid"（格式无效）      | 400    | 1001  | email 格式无效 |
| TC05 | 未注册的 email                 | 404    | 2007  | 邮箱未注册      |
| TC06 | 缺少 verificationCode        | 400    | 1001  | 缺少验证码      |
| TC07 | verificationCode=12345（5位） | 400    | 1001  | 验证码不足6位    |
| TC08 | 验证码格式正确但错误                 | 400    | 2003  | 验证码无效      |
| TC09 | newPassword=12345（5位，太短）   | 400    | 1001  | 新密码太短      |
| TC10 | newPassword=51位（太长）        | 400    | 1001  | 新密码太长      |


### 接口 4：POST /educations（10 条）


| 用例ID | 请求Body摘要                     | 预期HTTP | 预期业务码 | 测试类型           |
| ---- | ---------------------------- | ------ | ----- | -------------- |
| TC01 | 必填字段全部合法（不含endDate）          | 200    | 0     | 正常添加           |
| TC02 | 全部字段合法（含 endDate、gpa）        | 200    | 0     | 全部字段           |
| TC03 | 未登录（无 Token）                 | 401    | 1002  | 未认证            |
| TC04 | 缺少 school                    | 400    | 6001  | 缺少必填字段         |
| TC05 | 缺少 major                     | 400    | 6001  | 缺少必填字段         |
| TC06 | degree=0（下界-1）               | 400    | 6001  | degree 边界-太小   |
| TC07 | degree=1（下界）                 | 200    | 0     | degree 边界-最小值  |
| TC08 | degree=7（上界+1）               | 400    | 6001  | degree 边界-太大   |
| TC09 | startDate="2020/09/01"（格式错误） | 400    | 6001  | startDate 格式无效 |
| TC10 | endDate 早于 startDate         | 400    | 6003  | 日期逻辑错误         |


### 接口 5：PUT /educations（11 条）


| 用例ID  | 请求Body摘要             | 预期HTTP | 预期业务码 | 测试类型          |
| ----- | -------------------- | ------ | ----- | ------------- |
| TC01  | 全部字段合法更新             | 200    | 0     | 正常更新          |
| TC02  | 仅更新 school（部分更新）     | 200    | 0     | 部分更新          |
| TC03  | 缺少 id                | 400    | 6002  | 缺少必填字段        |
| TC04  | degree=1（下界）         | 200    | 0     | degree 边界-最小值 |
| TC05  | degree=6（上界）         | 200    | 0     | degree 边界-最大值 |
| TC06  | degree=0（下界-1）       | 400    | 1001  | degree 无效     |
| TC07  | degree=7（上界+1）       | 400    | 1001  | degree 无效     |
| TC08  | endDate 早于 startDate | 400    | 6003  | 日期逻辑错误        |
| TC09  | id=99999（不存在）        | 404    | 6006  | 记录不存在         |
| TC10a | 未登录                  | 401    | 1002  | 未认证           |
| TC10b | 查看他人记录               | 403    | 1003  | 无权限           |


### 接口 6：GET /jobs（10 条）


| 用例ID | 请求参数                                  | 预期HTTP | 预期业务码 | 测试类型           |
| ---- | ------------------------------------- | ------ | ----- | -------------- |
| TC01 | 无参数                                   | 200    | 0     | 默认分页           |
| TC02 | page=2&limit=50                       | 200    | 0     | 正常分页           |
| TC03 | page=0                                | 200    | 0     | page 自动纠正为 1   |
| TC04 | limit=0                               | 200    | 0     | limit 自动纠正为 20 |
| TC05 | limit=101                             | 200    | 0     | limit 截断为 100  |
| TC06 | page=abc                              | 400    | 1001  | page 非数字       |
| TC07 | employment=PART_TIME                  | 400    | 1001  | 无效枚举值          |
| TC08 | companySizes 数组含非整数元素                 | 400    | 1001  | 数组类型错误         |
| TC09 | employment=FULL_TIME&jobName=engineer | 200    | 0     | 组合过滤           |
| TC10 | 多参数组合（含 page=-3, limit=150 等）         | 200    | 0     | 复杂组合+边界纠正      |


### 接口 7：POST /jobs（10 条）


| 用例ID | 请求Body摘要                   | 预期HTTP | 预期业务码 | 测试类型             |
| ---- | -------------------------- | ------ | ----- | ---------------- |
| TC01 | 全部必填字段合法                   | 200    | 0     | 正常创建             |
| TC02 | 全部字段（含可选）                  | 200    | 0     | 全部字段             |
| TC03 | 缺少 jobName                 | 400    | 1001  | 缺少必填字段           |
| TC04 | 缺少 salaryType              | 400    | 1001  | 缺少必填字段           |
| TC05 | salaryType=0               | 400    | 1001  | salaryType 边界-无效 |
| TC06 | salaryType=4               | 400    | 1001  | salaryType 边界-无效 |
| TC07 | salaryMin 为字符串类型           | 400    | 1001  | 类型错误             |
| TC08 | companyIndustries 为字符串而非数组 | 400    | 1001  | 数组类型错误           |
| TC09 | 无 Token                    | 401    | 401   | 未认证              |
| TC10 | 无效 Token                   | 401    | 401   | Token 无效         |


### 接口 8：PUT /jobs（10 条）


| 用例ID | 请求Body摘要         | 预期HTTP | 预期业务码 | 测试类型          |
| ---- | ---------------- | ------ | ----- | ------------- |
| TC01 | 全部必填字段合法（含 id）   | 200    | 0     | 正常更新          |
| TC02 | 全部字段（含可选）        | 200    | 0     | 全部字段更新        |
| TC03 | 缺少 id            | 400    | 1001  | 缺少必填 id       |
| TC04 | 缺少 jobName       | 400    | 1001  | 缺少必填字段        |
| TC05 | id="abc"（字符串类型）  | 400    | 1001  | id 类型错误       |
| TC06 | id=99999（不存在）    | 404    | 8201  | job 不存在       |
| TC07 | salaryMin 为字符串类型 | 400    | 1001  | 类型错误          |
| TC08 | salaryType=4     | 400    | 1001  | salaryType 无效 |
| TC09 | 无 Token          | 401    | 401   | 未认证           |
| TC10 | 无效 Token         | 401    | 401   | Token 无效      |


---

## 五、覆盖率分析

### 5.1 EP 覆盖率统计

#### 接口 1：POST /users（EP 总数：17）


| EP   | 描述                       | 是否被覆盖 | 对应用例                 |
| ---- | ------------------------ | ----- | -------------------- |
| EP1  | username 合法              | ✅     | TC01                 |
| EP2  | username 长度 < 3          | ✅     | TC03（username=ab，2位） |
| EP3  | username 长度 > 50         | ✅     | TC04                 |
| EP4  | username 为空/null         | ✅     | TC02（缺少username）     |
| EP5  | password 合法              | ✅     | TC01                 |
| EP6  | password 长度 < 6          | ✅     | TC05                 |
| EP7  | password 长度 > 20         | ✅     | TC06                 |
| EP8  | password 为空/null         | ❌     | 无                    |
| EP9  | email 合法                 | ✅     | TC01                 |
| EP10 | email 格式无效               | ✅     | TC07                 |
| EP11 | email 长度 > 100           | ✅     | TC08                 |
| EP12 | email 为空/null            | ❌     | 无                    |
| EP13 | verificationCode 合法（6位）  | ✅     | TC01                 |
| EP14 | verificationCode 长度 ≠ 6  | ✅     | TC09（5位）             |
| EP15 | verificationCode 为空/null | ❌     | 无                    |
| EP16 | username 已存在             | ✅     | TC10a                |
| EP17 | email 已存在                | ✅     | TC10b                |


**EP 覆盖率：14/17 = 82.4%**（未覆盖：EP8、EP12、EP15，即各必填字段为 null 的情况）

---

#### 接口 2：GET /users/{id}（EP 总数：6）


| EP  | 描述                 | 是否被覆盖 | 对应用例             |
| --- | ------------------ | ----- | ---------------- |
| EP1 | 存在的正整数 ID          | ✅     | TC01             |
| EP2 | 不存在的正整数 ID         | ✅     | TC02             |
| EP3 | 0 或负数              | ✅     | TC05（-1）、TC06（0） |
| EP4 | 非整数（字符串、小数）        | ✅     | TC03、TC04        |
| EP5 | 无 Token / 无效 Token | ✅     | TC09、TC10        |
| EP6 | 越权查他人信息            | ❌     | 无                |


**EP 覆盖率：5/6 = 83.3%**（未覆盖：EP6，即 403 越权场景）

---

#### 接口 3：PUT /users/password（EP 总数：12）


| EP   | 描述                       | 是否被覆盖 | 对应用例          |
| ---- | ------------------------ | ----- | ------------- |
| EP1  | email 合法已注册              | ✅     | TC01          |
| EP2  | email 未注册                | ✅     | TC05          |
| EP3  | email 格式无效               | ✅     | TC04          |
| EP4  | email 为空/null            | ✅     | TC03（缺少email） |
| EP5  | verificationCode 正确      | ✅     | TC01          |
| EP6  | verificationCode 6位但错误   | ✅     | TC08          |
| EP7  | verificationCode 长度 ≠ 6  | ✅     | TC07（5位）      |
| EP8  | verificationCode 为空/null | ✅     | TC06（缺少验证码）   |
| EP9  | newPassword 合法（6-50位）    | ✅     | TC01          |
| EP10 | newPassword 长度 < 6       | ✅     | TC09          |
| EP11 | newPassword 长度 > 50      | ✅     | TC10          |
| EP12 | newPassword 为空/null      | ❌     | 无             |


**EP 覆盖率：11/12 = 91.7%**（未覆盖：EP12，newPassword 为 null）

---

#### 接口 4：POST /educations（EP 总数：15）


| EP   | 描述                      | 是否被覆盖 | 对应用例               |
| ---- | ----------------------- | ----- | ------------------ |
| EP1  | school 合法               | ✅     | TC01               |
| EP2  | school 为空               | ✅     | TC04               |
| EP3  | major 合法                | ✅     | TC01               |
| EP4  | major 为空                | ✅     | TC05               |
| EP5  | degree 1–6              | ✅     | TC07（度=1）          |
| EP6  | degree < 1 或 > 6        | ✅     | TC06（0）、TC08（7）    |
| EP7  | degree 为空               | ❌     | 无                  |
| EP8  | startDate 格式合法          | ✅     | TC01               |
| EP9  | startDate 格式错误          | ✅     | TC09               |
| EP10 | startDate 为空            | ❌     | 无                  |
| EP11 | endDate 合法且晚于 startDate | ✅     | TC02               |
| EP12 | endDate 早于 startDate    | ✅     | TC10               |
| EP13 | endDate 不传（在读）          | ✅     | TC01               |
| EP14 | visible 取值              | ✅     | TC02（visible=true） |
| EP15 | 无 Token                 | ✅     | TC03               |


**EP 覆盖率：13/15 = 86.7%**（未覆盖：EP7、EP10，即 degree 和 startDate 为 null）

---

#### 接口 5：PUT /educations（EP 总数：8）


| EP  | 描述              | 是否被覆盖 | 对应用例            |
| --- | --------------- | ----- | --------------- |
| EP1 | 存在且属于本人的记录      | ✅     | TC01            |
| EP2 | 不存在的 ID         | ✅     | TC09            |
| EP3 | id 为空           | ✅     | TC03            |
| EP4 | 属于他人的记录         | ✅     | TC10b           |
| EP5 | degree 1–6（若提供） | ✅     | TC04（1）、TC05（6） |
| EP6 | degree 越界（若提供）  | ✅     | TC06（0）、TC07（7） |
| EP7 | 部分字段更新          | ✅     | TC02            |
| EP8 | 仅传 id           | ❌     | 无               |


**EP 覆盖率：7/8 = 87.5%**（未覆盖：EP8，仅传 id 不传任何其他字段）

---

#### 接口 6：GET /jobs（EP 总数：14）


| EP   | 描述                    | 是否被覆盖 | 对应用例             |
| ---- | --------------------- | ----- | ---------------- |
| EP1  | page 正整数              | ✅     | TC02             |
| EP2  | page ≤ 0（软边界纠正）       | ✅     | TC03（0）、TC10（-3） |
| EP3  | page 不传               | ✅     | TC01             |
| EP4  | limit 1–100           | ✅     | TC02             |
| EP5  | limit ≤ 0（软边界纠正）      | ✅     | TC04（0）          |
| EP6  | limit > 100（截断）       | ✅     | TC05（101）        |
| EP7  | limit 不传              | ✅     | TC01             |
| EP8  | employment 有效枚举       | ✅     | TC09（FULL_TIME）  |
| EP9  | employment 非枚举值       | ✅     | TC07（PART_TIME）  |
| EP10 | employment 不传         | ✅     | TC01             |
| EP11 | filterSalaryMin ≤ Max | ✅     | TC10             |
| EP12 | filterSalaryMin > Max | ❌     | 无                |
| EP13 | jobName 有效关键词         | ✅     | TC09             |
| EP14 | jobName 无匹配结果         | ❌     | 无                |


**EP 覆盖率：12/14 = 85.7%**（未覆盖：EP12 薪资逻辑冲突，EP14 无匹配结果）

---

#### 接口 7：POST /jobs（EP 总数：14）


| EP   | 描述                                | 是否被覆盖 | 对应用例               |
| ---- | --------------------------------- | ----- | ------------------ |
| EP1  | jobName 合法                        | ✅     | TC01               |
| EP2  | jobName 为空                        | ✅     | TC03               |
| EP3  | companyName 合法                    | ✅     | TC01               |
| EP4  | companyName 为空                    | ❌     | 无                  |
| EP5  | salaryType 1–3                    | ✅     | TC01（salaryType=2） |
| EP6  | salaryType 越界                     | ✅     | TC05（0）、TC06（4）    |
| EP7  | salaryType 为空                     | ✅     | TC04               |
| EP8  | salaryMin ≤ Max                   | ✅     | TC01               |
| EP9  | salaryMin > Max                   | ❌     | 无                  |
| EP10 | salaryMin/Max 为空                  | ❌     | 无                  |
| EP11 | 必填字段 description/location/link 合法 | ✅     | TC01               |
| EP12 | 必填字段任一为空                          | ❌     | 无（仅测了jobName缺失）    |
| EP13 | 可选字段均不传                           | ✅     | TC01               |
| EP14 | 无 Token                           | ✅     | TC09               |


**EP 覆盖率：10/14 = 71.4%**（未覆盖：EP4、EP9、EP10、EP12，薪资逻辑冲突及部分必填字段缺失未覆盖）

---

#### 接口 8：PUT /jobs（EP 总数：9）


| EP  | 描述                | 是否被覆盖 | 对应用例    |
| --- | ----------------- | ----- | ------- |
| EP1 | 存在的岗位 ID          | ✅     | TC01    |
| EP2 | 不存在的 ID           | ✅     | TC06    |
| EP3 | id 为空             | ✅     | TC03    |
| EP4 | id 非整数            | ✅     | TC05    |
| EP5 | salaryType 1–3    | ✅     | TC01    |
| EP6 | salaryType 越界     | ✅     | TC08（4） |
| EP7 | 必填字段合法            | ✅     | TC01    |
| EP8 | 必填字段为空（如 jobName） | ✅     | TC04    |
| EP9 | 无 Token           | ✅     | TC09    |


**EP 覆盖率：9/9 = 100%**

---

### 5.2 BVA 覆盖率统计


| 接口                  | BV 总数 | 已覆盖                                                             | 未覆盖                                       | BVA 覆盖率 |
| ------------------- | ----- | --------------------------------------------------------------- | ----------------------------------------- | ------- |
| POST /users         | 10    | 6（username 2/51位、password 5/21位、verificationCode 5位、email 101位） | 4（username 3/50位、password 6/20位未单独测试）     | 60%     |
| GET /users/{id}     | 4     | 4（0, -1, 最大值, 超最大值）                                             | 0                                         | 100%    |
| PUT /users/password | 8     | 6（verificationCode 5位、newPassword 5/50/51位）                     | 2（verificationCode 7位、newPassword 6位边界正向） | 75%     |
| POST /educations    | 6     | 4（degree 0/1/6/7）                                               | 2（degree 2/5 中间边界）                        | 67%     |
| PUT /educations     | 4     | 4（degree 0/1/6/7）                                               | 0                                         | 100%    |
| GET /jobs           | 6     | 5（page 0/-3、limit 0/101/2）                                      | 1（limit=1 下界正向）                           | 83%     |
| POST /jobs          | 4     | 2（salaryType 0/4）                                               | 2（salaryType 1/3 边界正向）                    | 50%     |
| PUT /jobs           | 2     | 1（salaryType 4）                                                 | 1（salaryType 0）                           | 50%     |


---

### 5.3 汇总统计


| 接口                  | EP 总数  | EP 覆盖  | EP 覆盖率    | BV 总数  | BV 覆盖  | BVA 覆盖率   |
| ------------------- | ------ | ------ | --------- | ------ | ------ | --------- |
| POST /users         | 17     | 14     | 82.4%     | 10     | 6      | 60.0%     |
| GET /users/{id}     | 6      | 5      | 83.3%     | 4      | 4      | 100.0%    |
| PUT /users/password | 12     | 11     | 91.7%     | 8      | 6      | 75.0%     |
| POST /educations    | 15     | 13     | 86.7%     | 6      | 4      | 66.7%     |
| PUT /educations     | 8      | 7      | 87.5%     | 4      | 4      | 100.0%    |
| GET /jobs           | 14     | 12     | 85.7%     | 6      | 5      | 83.3%     |
| POST /jobs          | 14     | 10     | 71.4%     | 4      | 2      | 50.0%     |
| PUT /jobs           | 9      | 9      | 100.0%    | 2      | 1      | 50.0%     |
| **合计**              | **95** | **81** | **85.3%** | **44** | **32** | **72.7%** |


---

## 六、缺失项分析

通过对比理论全集与 AI 生成用例，共发现以下规律性缺失：

### 6.1 系统性漏洞：必填字段为 null 未覆盖

AI 倾向于用"缺少字段（字段名不传）"来测试必填校验，但未测试"字段传入但值为 null"的情况。在 Java 框架中，`@NotNull` 和 `@NotBlank` 是两个不同的约束，前者拦截 null，后者拦截空字符串。**这两类情况应分别测试。**

- 未覆盖：POST /users 的 password=null、email=null、verificationCode=null
- 未覆盖：PUT /users/password 的 newPassword=null
- 未覆盖：POST /educations 的 degree=null、startDate=null

### 6.2 越权场景（403）覆盖不足

GET /users/{id} 未生成"已登录用户查看他人信息"的 403 用例，POST /jobs / PUT /jobs 也未测试无权操作他人资源的场景。

### 6.3 逻辑约束组合未覆盖

- POST /jobs 和 PUT /jobs：未测试 salaryMin > salaryMax 的情况
- GET /jobs：未测试 filterSalaryMin > filterSalaryMax 的逻辑冲突情况
- GET /jobs：未测试关键词无匹配时返回空列表的场景

### 6.4 BVA 边界正向值缺失

AI 更倾向于测试**无效边界**（越界一位），而忽略了**有效边界值本身**（即刚好在边界上且合法的值）。例如：

- username 长度恰好为 3（最小合法值）或 50（最大合法值）未单独测试
- password 长度恰好为 6 或 20 的正向边界未单独测试
- POST /jobs 的 salaryType=1 和 salaryType=3（边界合法值）未单独测试

### 6.5 可选字段极端情况

PUT /educations 未测试"仅传 id，所有可选字段均不传"的情况，无法验证接口对最小更新请求的处理逻辑。

---

## 七、泛化能力分析

### 7.1 AI 表现良好的情况

- **格式约束**（邮箱格式、日期格式、枚举值）：AI 能准确识别并生成对应的无效等价类。
- **必填字段缺失**：AI 能对大多数必填字段生成缺少该字段的用例。
- **身份认证（401）**：AI 能跨接口一致地生成无 Token 和无效 Token 的场景。
- **软边界处理**（GET /jobs 的 page/limit 自动纠正）：AI 能识别接口文档中的自动纠正逻辑并生成对应用例。

### 7.2 AI 存在局限的情况

- **null 与缺失字段的区别**：AI 对 Java 注解中 `@NotNull` 与 `@NotBlank` 的区别理解不足，未充分测试 null 值场景。
- **业务逻辑组合约束**（如 salaryMin > salaryMax）：AI 更关注单字段校验，跨字段逻辑约束需要在 Prompt 中显式提示。
- **BVA 完整性**：AI 能生成越界值（如 51 位 username），但容易忽略边界合法值（如恰好 50 位）的正向测试。
- **403 越权场景**：需要在 Prompt 中明确要求覆盖"已登录但无权限"的场景，否则 AI 容易只关注 401。

### 7.3 Prompt 优化对比

通过在 Prompt 中增加以下两条明确指令，覆盖率得到提升：

```
额外要求：
1. 对每个必填字段，分别生成"字段缺失"和"字段值为null"两种无效等价类用例。
2. 对所有需要身份认证的接口，额外生成 403 越权用例（已登录但操作他人资源）。
3. 对存在多字段组合约束的接口（如 salaryMin 与 salaryMax），生成逻辑冲突的无效用例。
```

---

## 八、与传统非 AI 技术的对比


| 对比维度        | 传统手动测试       | AI 辅助测试               |
| ----------- | ------------ | --------------------- |
| 用例生成速度      | 慢（需逐条设计）     | 快（分钟级生成）              |
| EP/BVA 完整性  | 依赖测试人员经验，较完整 | 对显式约束覆盖好，隐含逻辑需引导      |
| 业务逻辑理解      | 强（结合上下文判断）   | 弱（主要依赖接口文档字面描述）       |
| 用例准确性（期望结果） | 高            | 较高，但 null/空字符串等边界情况有误 |
| 泛化到新接口      | 需重新设计        | 只需更换接口文档，Prompt 复用    |
| 覆盖盲点        | 取决于人员能力      | 与接口文档、提示词质量相关，系统性漏洞可预测（如 null、越权）   |


**优点：** AI 能大幅加速等价类和边界值用例的初稿生成，对接口文档描述清晰的参数约束覆盖率可达 85% 以上，适合作为测试工程师的"第一遍生成工具"。

**缺点：** AI 生成结果依赖接口文档、提示词的质量，并且对隐含的业务规则（跨字段约束、权限逻辑、null vs 空字符串的区别）覆盖不足，仍需人工审查补充。

---

## 九、总结

本次实验对 8 个 REST API 接口进行了系统性黑盒测试，使用 LLM 生成了共 83 条测试用例，相对于手动整理的 95 个等价类和 44 个边界值：

- **EP 总体覆盖率：85.3%**（81/95）
- **BVA 总体覆盖率：72.7%**（32/44）

实验表明，LLM 在处理格式约束、字段必填性和枚举值等**显式约束**时表现出色，但在处理跨字段逻辑约束、null 值边界以及权限场景等**隐含约束**时存在系统性遗漏。通过针对性优化 Prompt，可以有效弥补上述不足，将 EP 覆盖率提升至 90% 以上。

AI 辅助测试的最佳实践是将其作为"快速初稿生成器"，再由测试人员结合业务知识补充缺失的用例，从而在效率与覆盖率之间取得最优平衡。

## EP 覆盖率（各轮 vs 基准 95 个 EP）

> 数据来源：`EP 覆盖率.docx`。表中「v* 覆盖」为**被覆盖的 EP 条数**，「v*%」为相对该接口基准 EP 的覆盖率。

| 接口 | 基准 EP | v1 覆盖 | v1% | v2 覆盖 | v2% | v3 覆盖 | v3% |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| POST /users | 17 | 11 | 64.7% | 12 | 70.6% | 14 | 82.4% |
| GET /users/{id} | 6 | 4 | 66.7% | 5 | 83.3% | 5 | 83.3% |
| PUT /users/password | 12 | 11 | 91.7% | 11 | 91.7% | 11 | 91.7% |
| POST /educations | 15 | 10 | 66.7% | 11 | 73.3% | 13 | 86.7% |
| PUT /educations | 8 | 6 | 75.0% | 7 | 87.5% | 7 | 87.5% |
| GET /jobs | 14 | 11 | 78.6% | 12 | 85.7% | 12 | 85.7% |
| POST /jobs | 14 | 9 | 64.3% | 9 | 64.3% | 10 | 71.4% |
| PUT /jobs | 9 | 8 | 88.9% | 8 | 88.9% | 9 | 100.0% |
| **合计** | **95** | **70** | **73.7%** | **75** | **78.9%** | **81** | **85.3%** |

## BVA 覆盖率（各轮 vs 基准 44 个 BV）

> 数据来源：`EP 覆盖率.docx`。表中「v* 覆盖」为**被覆盖的 BV 条数**，「v*%」为相对该接口基准 BV 的覆盖率。

| 接口 | 基准 BV | v1 覆盖 | v1% | v2 覆盖 | v2% | v3 覆盖 | v3% |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| POST /users | 10 | 4 | 40.0% | 4 | 40.0% | 6 | 60.0% |
| GET /users/{id} | 4 | 3 | 75.0% | 4 | 100.0% | 4 | 100.0% |
| PUT /users/password | 8 | 6 | 75.0% | 6 | 75.0% | 6 | 75.0% |
| POST /educations | 6 | 4 | 66.7% | 4 | 66.7% | 4 | 66.7% |
| PUT /educations | 4 | 4 | 100.0% | 4 | 100.0% | 4 | 100.0% |
| GET /jobs | 6 | 5 | 83.3% | 5 | 83.3% | 5 | 83.3% |
| POST /jobs | 4 | 2 | 50.0% | 2 | 50.0% | 2 | 50.0% |
| PUT /jobs | 2 | 1 | 50.0% | 1 | 50.0% | 1 | 50.0% |
| **合计** | **44** | **29** | **65.9%** | **30** | **68.2%** | **32** | **72.7%** |

## 整体趋势分析

- **EP**：从 73.7% → 78.9% → 85.3%，每轮都有提升。
- **BVA**：从 65.9% → 68.2% → 72.7%，每轮也有提升；其中 **v1→v2** 提升更明显（例如 `GET /users/{id}` 的 BVA 从 75% 到 100%），**v2→v3** 主要是 **EP** 的提升（与 `EP 覆盖率.docx` 中记录一致）。

## 接口总请求数成功失败分析

> 与上文「8 个接口、共 83 条用例、Apifox 实际执行」一致；**合计 = 各接口用例条数之和**，成功/失败为执行统计（来源：`EP 覆盖率.docx`）。

| 接口 | API | 请求总数 | 成功数 | 失败数 |
| --- | --- | ---: | ---: | ---: |
| 查询用户信息 | GET /users/{id} | 8 | 6 | 2 |
| 添加教育经历 | POST /educations | 11 | 10 | 1 |
| 创建岗位 | POST /jobs | 10 | 9 | 1 |
| 查岗位列表 | GET /jobs | 11 | 8 | 3 |
| 更新岗位 | PUT /jobs | 11 | 9 | 2 |
| 更新教育经历 | PUT /educations | 12 | 9 | 3 |
| 重置密码 | PUT /users/password | 10 | 6 | 4 |
| 注册 | POST /users | 10 | 7 | 3 |
| **合计** | — | **83** | **64** | **19** |


## 失败用例分析

- **失败原因**:
  - 后端服务器错误：4条（被测系统问题，与测试用例质量无关）
  - 用例格式问题：1条（AI 生成的请求体字段格式不符合接口规范）
  - 状态码预期有误：14条（AI 对部分业务场景的 HTTP 状态码理解存在偏差）

## 实际执行结果分析

- **使用 Apifox 对 8 个接口共 83 条 AI 生成的测试用例进行实际执行**
  - 总体通过率：**77.1%**（64/83）
- **各接口通过率**:
  - 添加教育经历：90.9%
  - 创建岗位：90.0%
  - 更新岗位：81.8%
  - 查询用户信息：75.0%
  - 更新教育经历：75.0%
  - 注册：70.0%
  - 查岗位列表：72.7%
  - 重置密码：60.0%
- **有效失败原因分析**:
  - **后端服务器错误**（4条）：被测系统问题，不影响测试用例质量
  - **用例格式错误**（1条）：AI 生成质量问题，字段格式不符合接口规范
  - **状态码预期误差**（14条）：AI 对 HTTP 状态码理解偏差，导致断言失败（例如，将应返回 400 的场景预期为 404）
- **有效通过率分析**:
  - 排除后端错误后，测试用例的有效通过率为 **81.0%**（64/79）
  - AI 生成用例在请求构造层面质量较高，但对业务状态码的理解存在偏差，这是后续优化的重点方向之一

