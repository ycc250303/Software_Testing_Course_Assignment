# 基于 LLM 的 RESTful API 黑盒动态测试实验报告

> **LLM-Based Black-Box Testing for RESTful API Documentation**

Members: Chengcheng Yin,Qingyi Lu,Yining Sun
Instructor:Kaifeng Huang

---

## 目录

正文按**五章叙事**组织（与课堂汇报 / `presentation.html` 一致）；下表左列为汇报章节，右列为**本文原有节号**（便于检索与交叉引用，正文标题仍保留原编号）。


| 章（叙事）           | 内容说明                                                                                                                      | 对应正文节号            |
| --------------- | ------------------------------------------------------------------------------------------------------------------------- | ----------------- |
| 1 项目概述 & 实验方案设计 | 实验主题、目标、黑盒范围与对象；输入文档、工具、总体方案与评价维度                                                                                         | §1、§2、§3、§4       |
| 2 实验步骤          | 以 **GET /jobs** 为例展示 v1–v3 对话（prompt 与 LLM 输出）、**v4** 输入组合用例（§6.9）、**状态迁移**用例与 prompt（§9）；全文对话见附录 A                       | §5、§6.9、§9（及附录 A） |
| 3 实验结果分析        | **GET /jobs** 示例：EP/BVA 覆盖率计算过程；**全部接口** v1–v3 的 EP/BVA 覆盖率表（§7.4–7.5）；**EP/BVA 缺失项**归纳（§7.7）；**Apifox** v3 执行统计与失败归因（§8） | §7、§8             |
| 4 AI 能力分析       | AI 在黑盒测试中的作用、局限，与传统方案对比及改进思路                                                                                              | §10               |
| 5 总结            | 结论与后续工作方向                                                                                                                 | §11               |
| 附录 A            | 与 LLM 的完整对话记录                                                                                                             | 附录 A              |
| 附录 B            | 人工编写的 EP/BVA 全集                                                                                                           | 附录 B              |


**说明**：原 §9「状态迁移测试」的**设计过程**归入第 2 章叙事（与 LLM 对话）；**结果与讨论**可与第 3 章指标一并阅读。

---

## 1. 项目概述

本项目旨在探索基于大语言模型（LLM）的 RESTful API 黑盒测试设计与执行分析方法。

在接口数量较多且参数约束复杂的系统中，传统人工设计测试用例通常存在效率较低的问题，同时在跨字段逻辑约束、权限控制场景以及边界条件处理等方面，容易出现覆盖不充分或遗漏的情况。针对上述问题，本文引入大语言模型辅助测试用例设计，并结合人工复核与实际执行验证，以期在提升测试设计效率的同时降低漏测风险。

在实验设计方面，项目从接口文档中选取了 8 个具有代表性的 API 接口作为研究对象。基于黑盒测试方法中的等价类划分（Equivalence Partitioning, EP）与边界值分析（Boundary Value Analysis, BVA）生成测试用例，并通过实际接口调用对测试用例的可执行性与有效性进行验证。

本项目的核心工作包括：

- 基于接口文档的语义信息提取测试规则与参数约束；
- 利用大语言模型生成黑盒测试用例，并通过多轮迭代（v1–v3）持续优化生成质量；
- 基于 Apifox 工具执行测试用例并记录执行结果；
- 对等价类划分、边界值分析覆盖情况及执行结果进行统计分析，从而评估 LLM 在测试用例生成中的优势与局限性。

---

## 2. 输入（接口文档，文档需求描述）

本实验输入为项目接口文档：`API Document.md`。

该接口文档不仅包含参数校验规则，还覆盖了较为完整的业务功能描述，主要包括以下模块：

- 用户认证与账户管理：包括用户注册、登录、密码重置以及用户信息的查询与更新；
- 教育经历管理：包括教育经历的新增、更新、按用户查询、按 ID 查询与删除；
- 岗位信息管理：包括岗位创建、岗位更新、岗位详情查询以及岗位列表的筛选与分页查询；
- 鉴权与访问控制：包括基于 Bearer Token 的认证机制、部分接口的免登录访问策略，以及未认证/无权限场景的处理逻辑；
- 查询与过滤能力：岗位列表接口支持分页查询、多条件过滤以及边界自动修正（如 page、limit 参数的默认值与越界处理）。

在上述功能范围内，本实验选择了 8 个有代表性的接口作为输入子集：


| 编号  | 接口                | 方法   | 功能说明            |
| --- | ----------------- | ---- | --------------- |
| 1   | `/users`          | POST | 用户注册            |
| 2   | `/users/{id}`     | GET  | 根据 ID 查询用户信息    |
| 3   | `/users/password` | PUT  | 重置密码            |
| 4   | `/educations`     | POST | 添加教育经历          |
| 5   | `/educations`     | PUT  | 更新教育经历          |
| 6   | `/jobs`           | GET  | 查询岗位列表（分页与条件过滤） |
| 7   | `/jobs`           | POST | 创建岗位            |
| 8   | `/jobs`           | PUT  | 更新岗位            |


接口文档同时提供了本次测试所需的关键约束信息，包括：

- 请求参数定义与必填/可选约束；
- 鉴权要求（Bearer Token）；
- HTTP 状态码与业务码说明；
- 分页规则（如 `page`、`limit` 的默认值与边界处理）；
- 枚举值与格式约束（如邮箱格式、日期格式、枚举范围等）。

本实验在黑盒测试前提下，仅依赖接口文档及接口返回行为进行测试设计与验证，不涉及对系统内部实现逻辑或控制流的分析。

---

## 3. 工具使用情况

本项目主要使用以下工具：

- **LLM 工具**：GPT-5.3
  - **用途**：基于接口文档自动生成等价类划分（EP）、边界值分析（BVA）以及测试用例，并通过多轮提示词优化提升生成质量。
- **测试执行工具**：Apifox
  - **用途**：执行生成的接口测试用例，并记录 HTTP 状态码、业务码及测试结果（通过/失败）。

---

## 4. 实验设计

实验采用“基准集合 + LLM 多轮迭代 + 实际执行验证”的方式，具体步骤如下：

- 基准集合构建：
  - 人工根据接口文档整理理论测试基准集合，包括：
    - 等价类划分（EP）基准总数：95
    - 边界值分析（BVA）基准总数：44
- LLM 测试用例生成
  - 使用 LLM 针对各接口生成测试用例，并进行三轮迭代优化（v1、v2、v3）。
  - 对于多参数接口增加 v4 迭代，生成组合测试用例；
  - 针对登录态与 Token 生命周期补充一组状态迁移测试用例。
- 覆盖率统计与对比分析
  - 对每一轮生成结果分别统计 EP 与 BVA 的覆盖情况，分析不同迭代版本之间的变化趋势。
- 执行验证与结果分析
  - 选取最终版本（v3）测试用例，在 Apifox 中执行，并统计测试通过率及失败原因。

本文主要从以下三个维度对方法效果进行评估：

- 覆盖能力：等价类划分与边界值分析的覆盖程度；
- 用例质量：测试用例执行通过率及预期结果一致性；
- 工程效率：LLM 自动生成与人工修正相结合的可行性与效率提升。

---

## 5. 实验步骤

以下以 `GET /jobs` 为示例展示对话流程；8 个接口的完整对话记录详见附录 A。

### 5.1 示例输入（Input）

```text
System Overview:
The system under test is a RESTful job management API. The internal implementation is not visible. Testing is performed based on API specifications.

Functional Requirements:

R1. Query Job List:
The system provides an API endpoint GET /jobs to query a paginated list of jobs.

R2. Input Constraints:

page: optional, integer, default 1; if page <= 0, it is      automatically treated as 1
limit: optional, integer, default 20; if limit <= 0, it is      automatically treated as 20; if limit > 100, it is truncated to 100
companySizes: optional, array of integers
fundingTypes: optional, array of integers
industries: optional, array of strings
companyName: optional, string
employment: optional, enum {INTERN, FULL_TIME}
filterSalaryMin: optional, integer
filterSalaryMax: optional, integer
jobName: optional, string

R3. Business Rules:

If employment is not provided, there is no restriction
jobName is matched by inclusion
The endpoint supports pagination and filtering

R4. Error Handling:

Invalid parameter or type error → HTTP 400, code 1001

R5. Success Response:

HTTP 200
code = 0
data      contains list, total, page, limit, totalPages
```

### 5.2 v1

#### 5.2.1 **prompt**

```
You are a software testing expert.

Given the following system overview and requirements:

{Input}

Generate the result in a report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome

Boundary Value Analysis
Table columns: Field | Boundary Values

Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result

Generate exactly 10 test cases.
```

#### 5.2.2 output

**1) Equivalence Partitioning**


| ID   | Description                                                     | Outcome                                             |
| ---- | --------------------------------------------------------------- | --------------------------------------------------- |
| EP1  | No query parameters provided                                    | Valid, HTTP 200, defaults applied: page=1, limit=20 |
| EP2  | page is a positive integer                                      | Valid, HTTP 200                                     |
| EP3  | page is 0 or negative integer                                   | Valid, auto-corrected to 1, HTTP 200                |
| EP4  | page is non-integer or wrong type                               | Invalid, HTTP 400, code=1001                        |
| EP5  | limit is an integer between 1 and 100                           | Valid, HTTP 200                                     |
| EP6  | limit is 0 or negative integer                                  | Valid, auto-corrected to 20, HTTP 200               |
| EP7  | limit is greater than 100                                       | Valid, truncated to 100, HTTP 200                   |
| EP8  | limit is non-integer or wrong type                              | Invalid, HTTP 400, code=1001                        |
| EP9  | companySizes is an array of integers                            | Valid, HTTP 200                                     |
| EP10 | companySizes is not an array of integers                        | Invalid, HTTP 400, code=1001                        |
| EP11 | fundingTypes is an array of integers                            | Valid, HTTP 200                                     |
| EP12 | fundingTypes is not an array of integers                        | Invalid, HTTP 400, code=1001                        |
| EP13 | industries is an array of strings                               | Valid, HTTP 200                                     |
| EP14 | industries is not an array of strings                           | Invalid, HTTP 400, code=1001                        |
| EP15 | companyName is a string                                         | Valid, HTTP 200                                     |
| EP16 | companyName is not a string                                     | Invalid, HTTP 400, code=1001                        |
| EP17 | employment is INTERN or FULL_TIME                               | Valid, HTTP 200                                     |
| EP18 | employment is omitted                                           | Valid, no employment restriction, HTTP 200          |
| EP19 | employment is outside enum or wrong type                        | Invalid, HTTP 400, code=1001                        |
| EP20 | filterSalaryMin / filterSalaryMax are integers                  | Valid, HTTP 200                                     |
| EP21 | filterSalaryMin / filterSalaryMax are non-integer or wrong type | Invalid, HTTP 400, code=1001                        |
| EP22 | jobName is a string                                             | Valid, inclusion match applied, HTTP 200            |
| EP23 | jobName is not a string                                         | Invalid, HTTP 400, code=1001                        |
| EP24 | Valid combination of pagination and filters                     | Valid, HTTP 200, filtered paginated result returned |


**2) Boundary Value Analysis**


| Field           | Boundary Values                                                                                |
| --------------- | ---------------------------------------------------------------------------------------------- |
| page            | -1, 0, 1, 2                                                                                    |
| limit           | -1, 0, 1, 20, 100, 101                                                                         |
| companySizes    | empty array, single integer element, multiple integer elements, array with non-integer element |
| fundingTypes    | empty array, single integer element, multiple integer elements, array with non-integer element |
| industries      | empty array, single string element, multiple string elements, array with non-string element    |
| companyName     | empty string, 1-character string, typical string, non-string                                   |
| employment      | omitted, INTERN, FULL_TIME, invalid enum value                                                 |
| filterSalaryMin | negative integer, 0, positive integer, non-integer                                             |
| filterSalaryMax | negative integer, 0, positive integer, non-integer                                             |
| jobName         | empty string, 1-character string, typical substring, non-string                                |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                           | Expected Result                                                                      |
| ------------ | -------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| TC01         | Call GET /jobs with no parameters                                                                  | HTTP 200; code=0; data contains list, total, page=1, limit=20, totalPages            |
| TC02         | Call GET /jobs?page=2&limit=50                                                                     | HTTP 200; code=0; response shows page=2, limit=50; data structure is correct         |
| TC03         | Call GET /jobs?page=0&limit=0                                                                      | HTTP 200; code=0; system auto-corrects to page=1, limit=20                           |
| TC04         | Call GET /jobs?page=-5&limit=-10                                                                   | HTTP 200; code=0; system auto-corrects to page=1, limit=20                           |
| TC05         | Call GET /jobs?limit=101                                                                           | HTTP 200; code=0; system truncates limit to 100                                      |
| TC06         | Call GET /jobs?page=abc                                                                            | HTTP 400; code=1001                                                                  |
| TC07         | Call GET /jobs?employment=PART_TIME                                                                | HTTP 400; code=1001                                                                  |
| TC08         | Call GET /jobs?companySizes=small or companySizes=[1,"a"]                                          | HTTP 400; code=1001                                                                  |
| TC09         | Call GET /jobs?employment=FULL_TIME&jobName=engineer&companyName=Tech&industries=["AI"]            | HTTP 200; code=0; returned jobs satisfy filters; jobName uses inclusion match        |
| TC10         | Call GET /jobs?filterSalaryMin=5000&filterSalaryMax=10000&fundingTypes=[1,2]&companySizes=[50,100] | HTTP 200; code=0; filtered paginated result returned with correct response structure |


**v1 小结**

特点：可快速生成主干用例，但对 null 场景、越权场景、跨字段逻辑覆盖不足。

### 5.3 v2

在 v1 结果基础上，我们发现模型虽然能覆盖大部分显式参数校验，但输出仍偏“基础校验导向”，对边界表达的一致性、业务错误场景的结构化表达不够稳定。
因此 v2 的目标不是增加结果数量，而是先提升输出结构质量：让等价类和边界项更完整、更统一，便于后续映射到可执行测试用例。

#### 5.3.1 **prompt**

```
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a structured testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome
Include both valid and invalid equivalence classes.

Boundary Value Analysis
Table columns: Field | Boundary Values
Include boundary values for pagination and filter-related parameters, especially page, limit, and employment.

Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result

Requirements:

Cover valid inputs, corrected boundary cases, invalid formats,invalid enum values, and filter scenarios
Generate exactly 10 test cases
Keep output concise and structured
```

#### 5.3.2 **output**

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                        | Outcome                                             |
| ---- | -------------------------------------------------- | --------------------------------------------------- |
| EP1  | No parameters provided                             | Valid, HTTP 200, defaults applied: page=1, limit=20 |
| EP2  | page is a positive integer                         | Valid, HTTP 200                                     |
| EP3  | page is 0 or negative integer                      | Valid, corrected to 1, HTTP 200                     |
| EP4  | page is non-integer or wrong type                  | Invalid, HTTP 400, code=1001                        |
| EP5  | limit is integer in range 1..100                   | Valid, HTTP 200                                     |
| EP6  | limit is 0 or negative integer                     | Valid, corrected to 20, HTTP 200                    |
| EP7  | limit is greater than 100                          | Valid, truncated to 100, HTTP 200                   |
| EP8  | limit is non-integer or wrong type                 | Invalid, HTTP 400, code=1001                        |
| EP9  | companySizes is an array of integers               | Valid, HTTP 200                                     |
| EP10 | companySizes contains non-integer or is wrong type | Invalid, HTTP 400, code=1001                        |
| EP11 | fundingTypes is an array of integers               | Valid, HTTP 200                                     |
| EP12 | fundingTypes contains non-integer or is wrong type | Invalid, HTTP 400, code=1001                        |
| EP13 | industries is an array of strings                  | Valid, HTTP 200                                     |
| EP14 | industries contains non-string or is wrong type    | Invalid, HTTP 400, code=1001                        |
| EP15 | companyName is a string                            | Valid, HTTP 200                                     |
| EP16 | companyName is wrong type                          | Invalid, HTTP 400, code=1001                        |
| EP17 | employment is INTERN or FULL_TIME                  | Valid, HTTP 200                                     |
| EP18 | employment is omitted                              | Valid, no employment restriction, HTTP 200          |
| EP19 | employment has invalid enum value or wrong type    | Invalid, HTTP 400, code=1001                        |
| EP20 | filterSalaryMin / filterSalaryMax are integers     | Valid, HTTP 200                                     |
| EP21 | filterSalaryMin / filterSalaryMax are wrong type   | Invalid, HTTP 400, code=1001                        |
| EP22 | jobName is a string                                | Valid, inclusion match, HTTP 200                    |
| EP23 | jobName is wrong type                              | Invalid, HTTP 400, code=1001                        |
| EP24 | Multiple valid filters used together               | Valid, HTTP 200, filtered paginated result          |


**2) Boundary Value Analysis**


| Field           | Boundary Values                                                           |
| --------------- | ------------------------------------------------------------------------- |
| page            | -1, 0, 1, 2                                                               |
| limit           | -1, 0, 1, 20, 100, 101                                                    |
| employment      | omitted, INTERN, FULL_TIME, invalid value such as PART_TIME               |
| companySizes    | empty array, one integer, multiple integers, array containing non-integer |
| fundingTypes    | empty array, one integer, multiple integers, array containing non-integer |
| industries      | empty array, one string, multiple strings, array containing non-string    |
| companyName     | empty string, single-character string, normal string, non-string          |
| filterSalaryMin | negative integer, 0, positive integer, non-integer                        |
| filterSalaryMax | negative integer, 0, positive integer, non-integer                        |
| jobName         | empty string, single-character string, normal substring, non-string       |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                   | Expected Result                                                                    |
| ------------ | ---------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| TC01         | GET /jobs with no parameters                                                                               | HTTP 200, code=0, data contains list, total, page=1, limit=20, totalPages          |
| TC02         | GET /jobs?page=2&limit=50                                                                                  | HTTP 200, code=0, response shows page=2, limit=50                                  |
| TC03         | GET /jobs?page=0&limit=0                                                                                   | HTTP 200, code=0, corrected to page=1, limit=20                                    |
| TC04         | GET /jobs?page=-3&limit=101                                                                                | HTTP 200, code=0, corrected to page=1, truncated to limit=100                      |
| TC05         | GET /jobs?page=abc                                                                                         | HTTP 400, code=1001                                                                |
| TC06         | GET /jobs?limit=1.5                                                                                        | HTTP 400, code=1001                                                                |
| TC07         | GET /jobs?employment=PART_TIME                                                                             | HTTP 400, code=1001                                                                |
| TC08         | GET /jobs?companySizes=[1,"x"]                                                                             | HTTP 400, code=1001                                                                |
| TC09         | GET /jobs?employment=FULL_TIME&jobName=engineer                                                            | HTTP 200, code=0, returned jobs match FULL_TIME; jobName is matched by inclusion   |
| TC10         | GET /jobs?companyName=Tech&industries=["AI"]&fundingTypes=[1,2]&filterSalaryMin=5000&filterSalaryMax=10000 | HTTP 200, code=0, filtered paginated result returned with valid response structure |


**v2 小结**

特点：边界值与鉴权负例覆盖提升，但隐含逻辑约束仍有缺口。

### 5.4 v3

经过 v2 后，输出结构已更清晰，但仍存在“规则覆盖有、测试语义不够严格”的问题，例如部分场景描述不够可执行、规则到用例映射关系不够明确。
因此 v3 继续迭代的重点是提高测试语义质量：强调无重复、逐条对应规则、并严格约束输出只基于给定需求，减少模型自由发挥带来的歧义。

#### 5.4.1 **prompt**

```
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a formal software testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome

Clearly distinguish valid and invalid classes
Cover constraints: pagination defaults, pagination correction rules, enum validation, type validation, and filtering behavior

Boundary Value Analysis
Table columns: Field | Boundary Values

Include meaningful boundary values
page: negative, zero, one, typical positive
limit: negative, zero, default, upper bound 100, above upper      bound
employment: valid enum values and invalid enum values

Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result

Requirements:

Use ONLY the provided requirements
Cover: default pagination, corrected page/limit values, upper      bound truncation, valid filters, invalid enum/type input, and combined      query conditions
Generate exactly 10 high-quality test cases (no duplicates)
Each test case must correspond to a rule or boundary condition
Keep output concise, table-based, and suitable for a report
```

#### 5.4.2 **output**

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                               | Outcome                                            |
| ---- | --------------------------------------------------------- | -------------------------------------------------- |
| EP1  | No query parameters provided                              | Valid — HTTP 200, code=0, default page=1, limit=20 |
| EP2  | page is a positive integer                                | Valid — HTTP 200                                   |
| EP3  | page is 0 or negative integer                             | Valid — corrected to 1, HTTP 200                   |
| EP4  | page is non-integer or wrong type                         | Invalid — HTTP 400, code=1001                      |
| EP5  | limit is integer within 1..100                            | Valid — HTTP 200                                   |
| EP6  | limit is 0 or negative integer                            | Valid — corrected to 20, HTTP 200                  |
| EP7  | limit is greater than 100                                 | Valid — truncated to 100, HTTP 200                 |
| EP8  | limit is non-integer or wrong type                        | Invalid — HTTP 400, code=1001                      |
| EP9  | employment is INTERN or FULL_TIME                         | Valid — HTTP 200                                   |
| EP10 | employment is omitted                                     | Valid — no employment restriction, HTTP 200        |
| EP11 | employment is invalid enum or wrong type                  | Invalid — HTTP 400, code=1001                      |
| EP12 | companySizes is an array of integers                      | Valid — HTTP 200                                   |
| EP13 | companySizes is wrong type or contains non-integer values | Invalid — HTTP 400, code=1001                      |
| EP14 | fundingTypes is an array of integers                      | Valid — HTTP 200                                   |
| EP15 | fundingTypes is wrong type or contains non-integer values | Invalid — HTTP 400, code=1001                      |
| EP16 | industries is an array of strings                         | Valid — HTTP 200                                   |
| EP17 | industries is wrong type or contains non-string values    | Invalid — HTTP 400, code=1001                      |
| EP18 | companyName is a string                                   | Valid — HTTP 200                                   |
| EP19 | companyName is wrong type                                 | Invalid — HTTP 400, code=1001                      |
| EP20 | filterSalaryMin and filterSalaryMax are integers          | Valid — HTTP 200                                   |
| EP21 | filterSalaryMin or filterSalaryMax is wrong type          | Invalid — HTTP 400, code=1001                      |
| EP22 | jobName is a string                                       | Valid — inclusion match applied, HTTP 200          |
| EP23 | jobName is wrong type                                     | Invalid — HTTP 400, code=1001                      |
| EP24 | Multiple valid filters and pagination used together       | Valid — HTTP 200, filtered paginated result        |


**2) Boundary Value Analysis**


| Field           | Boundary Values                                                     |
| --------------- | ------------------------------------------------------------------- |
| page            | -1, 0, 1, 2                                                         |
| limit           | -1, 0, 20, 100, 101                                                 |
| employment      | omitted, INTERN, FULL_TIME, PART_TIME                               |
| companySizes    | empty array, one integer, multiple integers, array with non-integer |
| fundingTypes    | empty array, one integer, multiple integers, array with non-integer |
| industries      | empty array, one string, multiple strings, array with non-string    |
| companyName     | empty string, typical string, non-string                            |
| filterSalaryMin | negative integer, 0, positive integer, non-integer                  |
| filterSalaryMax | negative integer, 0, positive integer, non-integer                  |
| jobName         | empty string, one-character string, typical substring, non-string   |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                          | Expected Result                                                                                                                 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| TC01         | Send GET /jobs with no parameters                                                                                                 | HTTP 200, code=0; response contains data.list, data.total, data.page=1, data.limit=20, data.totalPages                          |
| TC02         | Send GET /jobs?page=2&limit=50                                                                                                    | HTTP 200, code=0; response shows page=2, limit=50                                                                               |
| TC03         | Send GET /jobs?page=0                                                                                                             | HTTP 200, code=0; page is corrected to 1                                                                                        |
| TC04         | Send GET /jobs?limit=0                                                                                                            | HTTP 200, code=0; limit is corrected to 20                                                                                      |
| TC05         | Send GET /jobs?limit=101                                                                                                          | HTTP 200, code=0; limit is truncated to 100                                                                                     |
| TC06         | Send GET /jobs?page=abc                                                                                                           | HTTP 400, code=1001                                                                                                             |
| TC07         | Send GET /jobs?employment=PART_TIME                                                                                               | HTTP 400, code=1001                                                                                                             |
| TC08         | Send GET /jobs?companySizes=[1,"x"]                                                                                               | HTTP 400, code=1001                                                                                                             |
| TC09         | Send GET /jobs?employment=FULL_TIME&jobName=engineer                                                                              | HTTP 200, code=0; results are restricted to FULL_TIME; jobName is matched by inclusion                                          |
| TC10         | Send GET /jobs?page=-3&limit=150&companyName=Tech&industries=["AI"]&fundingTypes=[1,2]&filterSalaryMin=5000&filterSalaryMax=10000 | HTTP 200, code=0; page corrected to 1, limit truncated to 100; filtered paginated result returned with valid response structure |


**v3 小结**

---

特点：补齐更多隐含规则与组合逻辑场景，形成最终执行版本。

### 5.5 v4

在 v4 中，针对 **GET /jobs** 接口进行了更多的测试覆盖。v4 版本聚焦于更细化的规则和业务场景，并结合输入组合测试，重点验证了接口在复杂参数交互下的表现。

#### 5.5.1 Prompt

```
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a formal software testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome
Clearly distinguish valid and invalid classes.
Cover constraints: pagination defaults, pagination correction rules, enum validation, type validation, and filtering behavior.
Boundary Value Analysis
Table columns: Field | Boundary Values
Include meaningful boundary values.
page: negative, zero, one, typical positive
limit: negative, zero, default, upper bound 100, above upper bound
employment: valid enum values and invalid enum values
filterSalaryMin, filterSalaryMax: boundary values (0, positive, large numbers)
jobName: empty string, single character, long string
Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result

Requirements:

Use ONLY the provided requirements
Cover: normal case, missing fields, boundary values, invalid formats, duplicate username/email, and invalid verification code
Generate exactly 10 high-quality test cases (no duplicates)
Each test case must correspond to a rule or boundary condition
Keep output concise, table-based, and suitable for a report
```

#### 5.5.2 output

1. Equivalence Partitioning


| ID   | Description                                                                                                     | Outcome                        |
| ---- | --------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| EP01 | All fields provided with valid values; username and email are unique; verificationCode is valid and not expired | Valid                          |
| EP02 | username missing                                                                                                | Invalid -> HTTP 400, code 1001 |
| EP03 | username length < 3                                                                                             | Invalid -> HTTP 400, code 1001 |
| EP04 | username length 3-50                                                                                            | Valid                          |
| EP05 | username length > 50                                                                                            | Invalid -> HTTP 400, code 1001 |
| EP06 | password missing                                                                                                | Invalid -> HTTP 400, code 1001 |
| EP07 | password length < 6                                                                                             | Invalid -> HTTP 400, code 1001 |
| EP08 | password length 6-20                                                                                            | Valid                          |
| EP09 | password length > 20                                                                                            | Invalid -> HTTP 400, code 1001 |
| EP10 | email missing                                                                                                   | Invalid -> HTTP 400, code 1001 |
| EP11 | email format valid and length <= 100                                                                            | Valid                          |
| EP12 | email format invalid                                                                                            | Invalid -> HTTP 400, code 1001 |
| EP13 | email length > 100                                                                                              | Invalid -> HTTP 400, code 1001 |
| EP14 | verificationCode missing                                                                                        | Invalid -> HTTP 400, code 1001 |
| EP15 | verificationCode is exactly 6 digits                                                                            | Valid format class             |
| EP16 | verificationCode length != 6                                                                                    | Invalid -> HTTP 400, code 1001 |
| EP17 | verificationCode contains non-digit characters                                                                  | Invalid -> HTTP 400, code 1001 |
| EP18 | username already exists                                                                                         | Invalid -> HTTP 400, code 2001 |
| EP19 | email already exists                                                                                            | Invalid -> HTTP 400, code 2002 |
| EP20 | verificationCode invalid or expired                                                                             | Invalid -> HTTP 400, code 2003 |


1. Boundary Value Analysis


| Field            | Boundary Values                                                    |
| ---------------- | ------------------------------------------------------------------ |
| username         | 2, 3, 4, 49, 50, 51 characters                                     |
| password         | 5, 6, 7, 19, 20, 21 characters                                     |
| email            | missing, valid format, invalid format, 99, 100, 101 characters     |
| verificationCode | missing, 5 digits, 6 digits, 7 digits, 6 characters with non-digit |


1. Sample Test Cases


| Test Case ID | Scenario                                                                                   | Expected Result     |
| ------------ | ------------------------------------------------------------------------------------------ | ------------------- |
| TC01         | Register with valid username/password/email and valid non-expired 6-digit verificationCode | HTTP 200, code=0    |
| TC02         | Register with username missing                                                             | HTTP 400, code=1001 |
| TC03         | Register with username length=2                                                            | HTTP 400, code=1001 |
| TC04         | Register with username length=51                                                           | HTTP 400, code=1001 |
| TC05         | Register with password length=5                                                            | HTTP 400, code=1001 |
| TC06         | Register with password length=21                                                           | HTTP 400, code=1001 |
| TC07         | Register with email in invalid format                                                      | HTTP 400, code=1001 |
| TC08         | Register with verificationCode containing 6 characters with a non-digit                    | HTTP 400, code=1001 |
| TC09         | Register with an existing username and otherwise valid inputs                              | HTTP 400, code=2001 |
| TC10         | Register with a new username and email, but an invalid or expired verificationCode         | HTTP 400, code=2003 |


### 5.6 v4 总结

在 v4 中，**GET /jobs** 接口的测试进行了进一步优化和迭代，重点加强了输入组合测试（Input Combination Testing）的应用，针对多参数交互场景进行了全面的覆盖。与 v3 版本相比，v4 通过更加严格的规则映射和参数组合，提高了测试的完整性和执行效果。

#### 5.6.1 主要改进

- **输入组合测试**：重点测试多个参数组合的交互行为，确保每个测试用例不仅仅验证单一参数，而是涵盖 2-4 个参数的组合及其相互作用。
- **更加严谨的规则映射**：每个测试用例严格对应于某一条规则，减少了测试用例间的重复和不必要的重叠。
- **增强的语义严谨性**：进一步减少了模型生成中的自由发挥，确保每个测试用例的输出更加清晰和可执行，避免了测试语义的歧义。

#### 5.6.2 关键特性

- **多参数交互**：在 v4 中，通过组合多个查询参数（如 `employment`, `filterSalaryMin`, `filterSalaryMax`, `jobName` 等），测试了这些参数间的复杂交互，确保接口能够正确处理各种参数组合。
- **规则与边界条件严格对应**：每个测试用例都与规则和边界条件直接相关，避免了无关测试或遗漏场景。
- **减少歧义**：相比于 v3，v4 版本的测试语义更加明确，生成的测试用例能够准确反映实际的业务需求。

### 5.7 小结

v4 版本通过在测试中引入更多的交互组合逻辑，进一步增强了对接口的测试覆盖，确保了接口在复杂场景下的正确性和鲁棒性。同时，v4 强化了规则到用例的映射关系，提高了测试的精确性和可执行性。

---

## 6. LLM 生成的测试用例

以下为 LLM 针对 8 个接口生成的全部测试用例，共 83 条。

### 6.1 POST /users


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


### 6.2 GET /users/ {id}


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


### 6.3 PUT /users/password


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


### 6.4 POST /educations


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


### 6.5 PUT /educations


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


### 6.6 GET /jobs


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


### 6.7 POST /jobs

下表汇总了6.7 POST /jobs的关键数据与分类结果，便于后续对比与分析。


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


### 6.8 PUT /jobs

下表汇总了6.8 PUT /jobs的关键数据与分类结果，便于后续对比与分析。


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


### 6.9 GET /jobs（v4 输入组合测试）


| Test Case ID | Scenario (with parameter combinations)                                                                          | Expected HTTP Status | Expected Business Code | Covered Rules                                                               |
| ------------ | --------------------------------------------------------------------------------------------------------------- | -------------------- | ---------------------- | --------------------------------------------------------------------------- |
| TC-01        | page=-5, limit=0 (both corrected to 1 and 20), employment=INTERN, jobName="developer"                           | 200                  | 0                      | R2: page≤0→1, limit≤0→20; R3: employment filter + jobName inclusion         |
| TC-02        | page=2, limit=150 (truncated to 100), filterSalaryMin=50000, filterSalaryMax=100000                             | 200                  | 0                      | R2: limit>100→100; R3: salary range filter                                  |
| TC-03        | page=1, limit=100, employment=FULL_TIME, companyName="Tech", filterSalaryMin=0                                  | 200                  | 0                      | R3: multiple filters combined (employment, companyName, salary)             |
| TC-04        | page=0, limit=20, industries=["finance","healthcare"], jobName="data", fundingTypes=[1,2]                       | 200                  | 0                      | R2: page=0→1; R3: industries array + fundingTypes array + jobName inclusion |
| TC-05        | page=3, limit=50, filterSalaryMin=80000, filterSalaryMax=120000, employment=INTERN                              | 200                  | 0                      | R3: salary range + employment type; valid boundary values                   |
| TC-06        | page=1, limit=101 (→100), companySizes=[10,50,100], jobName="", filterSalaryMin=1                               | 200                  | 0                      | R2: limit truncation; R3: empty jobName (matches all), companySizes filter  |
| TC-07        | page="invalid" (string), limit=20, employment=FULL_TIME                                                         | 400                  | 1001                   | R4: invalid page type (string instead of integer)                           |
| TC-08        | page=1, limit="abc" (string), jobName="engineer", filterSalaryMin=30000                                         | 400                  | 1001                   | R4: invalid limit type (string)                                             |
| TC-09        | page=1, limit=20, employment="PART_TIME" (invalid enum), companyName="ABC"                                      | 400                  | 1001                   | R4: invalid enum value for employment                                       |
| TC-10        | page=1, limit=20, companySizes="not_an_array" (string), fundingTypes=[1,2]                                      | 400                  | 1001                   | R4: invalid type for companySizes (string instead of array)                 |
| TC-11        | page=2, limit=30, industries="tech" (string instead of array), jobName="manager"                                | 400                  | 1001                   | R4: invalid type for industries (string vs array)                           |
| TC-12        | page=1, limit=20, filterSalaryMin="high" (non-integer), filterSalaryMax=100000, employment=FULL_TIME            | 400                  | 1001                   | R4: invalid type for filterSalaryMin                                        |
| TC-13        | page=5, limit=75, fundingTypes=[1,"two",3] (mixed types), jobName="analyst", filterSalaryMin=40000              | 400                  | 1001                   | R4: fundingTypes contains non-integer element                               |
| TC-14        | page=1, limit=default (omitted), filterSalaryMin=100000, filterSalaryMax=50000 (min > max), industries=["tech"] | 200                  | 0                      | R2: limit default=20; R3: min>max returns empty list                        |
| TC-15        | page=999999, limit=100, companyName="", jobName="senior", employment=INTERN, companySizes=[100]                 | 200                  | 0                      | R3: very large page number (beyond total pages), multiple filters combined  |


---

## 7. EP/BVA 覆盖率分析

本章节从整体与分接口两个层面对实验结果进行分析，包括：v3 版本测试用例相对于人工构建的 EP/BVA 基准集合的覆盖情况、各接口的统计汇总结果，以及 v1–v3 多轮迭代过程中覆盖率的变化趋势。同时，针对未覆盖项进行系统性归纳，以分析 LLM 在测试用例生成中的局限性。

人工整理的 EP/BVA 基准全集（即各接口的等价类与边界值条目）详见附录 B；本章所有统计分析中的“基准”均指该附录内容。

### 7.1 EP 覆盖率分析

本小节统计了 LLM 在 v3 版本生成的测试用例的 EP 覆盖率情况。

#### 7.1.1 POST /users


| EP   | 描述                       | 是否被覆盖 | 对应用例                 |
| ---- | ------------------------ | ----- | -------------------- |
| EP1  | username 合法              | 是     | TC01                 |
| EP2  | username 长度 < 3          | 是     | TC03（username=ab，2位） |
| EP3  | username 长度 > 50         | 是     | TC04                 |
| EP4  | username 为空/null         | 是     | TC02（缺少username）     |
| EP5  | password 合法              | 是     | TC01                 |
| EP6  | password 长度 < 6          | 是     | TC05                 |
| EP7  | password 长度 > 20         | 是     | TC06                 |
| EP8  | password 为空/null         | 否     | 无                    |
| EP9  | email 合法                 | 是     | TC01                 |
| EP10 | email 格式无效               | 是     | TC07                 |
| EP11 | email 长度 > 100           | 是     | TC08                 |
| EP12 | email 为空/null            | 否     | 无                    |
| EP13 | verificationCode 合法（6位）  | 是     | TC01                 |
| EP14 | verificationCode 长度 ≠ 6  | 是     | TC09（5位）             |
| EP15 | verificationCode 为空/null | 否     | 无                    |
| EP16 | username 已存在             | 是     | TC10a                |
| EP17 | email 已存在                | 是     | TC10b                |


该接口等价类覆盖率为 82.4%，未覆盖项主要集中在必填字段显式为 null 的场景。

---

#### 7.1.2 GET /users/ {id}


| EP  | 描述                 | 是否被覆盖 | 对应用例             |
| --- | ------------------ | ----- | ---------------- |
| EP1 | 存在的正整数 ID          | 是     | TC01             |
| EP2 | 不存在的正整数 ID         | 是     | TC02             |
| EP3 | 0 或负数              | 是     | TC05（-1）、TC06（0） |
| EP4 | 非整数（字符串、小数）        | 是     | TC03、TC04        |
| EP5 | 无 Token / 无效 Token | 是     | TC09、TC10        |
| EP6 | 越权查他人信息            | 否     | 无                |


该接口等价类覆盖率为 83.3%，未覆盖项为用户越权操作场景。

---

#### 7.1.3 PUT /users/password


| EP   | 描述                       | 是否被覆盖 | 对应用例          |
| ---- | ------------------------ | ----- | ------------- |
| EP1  | email 合法已注册              | 是     | TC01          |
| EP2  | email 未注册                | 是     | TC05          |
| EP3  | email 格式无效               | 是     | TC04          |
| EP4  | email 为空/null            | 是     | TC03（缺少email） |
| EP5  | verificationCode 正确      | 是     | TC01          |
| EP6  | verificationCode 6位但错误   | 是     | TC08          |
| EP7  | verificationCode 长度 ≠ 6  | 是     | TC07（5位）      |
| EP8  | verificationCode 为空/null | 是     | TC06（缺少验证码）   |
| EP9  | newPassword 合法（6-50位）    | 是     | TC01          |
| EP10 | newPassword 长度 < 6       | 是     | TC09          |
| EP11 | newPassword 长度 > 50      | 是     | TC10          |
| EP12 | newPassword 为空/null      | 否     | 无             |


该接口等价类覆盖率为 91.7%，未覆盖项为 newPassword 参数为空或者 null 的场景

---

#### 7.1.4 POST /educations


| EP   | 描述                      | 是否被覆盖 | 对应用例               |
| ---- | ----------------------- | ----- | ------------------ |
| EP1  | school 合法               | 是     | TC01               |
| EP2  | school 为空               | 是     | TC04               |
| EP3  | major 合法                | 是     | TC01               |
| EP4  | major 为空                | 是     | TC05               |
| EP5  | degree 1–6              | 是     | TC07（度=1）          |
| EP6  | degree < 1 或 > 6        | 是     | TC06（0）、TC08（7）    |
| EP7  | degree 为空               | 否     | 无                  |
| EP8  | startDate 格式合法          | 是     | TC01               |
| EP9  | startDate 格式错误          | 是     | TC09               |
| EP10 | startDate 为空            | 否     | 无                  |
| EP11 | endDate 合法且晚于 startDate | 是     | TC02               |
| EP12 | endDate 早于 startDate    | 是     | TC10               |
| EP13 | endDate 不传（在读）          | 是     | TC01               |
| EP14 | visible 取值              | 是     | TC02（visible=true） |
| EP15 | 无 Token                 | 是     | TC03               |


该接口等价类覆盖率为 86.7%，未覆盖项为 degree 和 startDate 参数为空的场景。

---

#### 7.1.5 PUT /educations

下表汇总了接口 5：PUT /educations的关键数据与分类结果，便于后续对比与分析。


| EP  | 描述              | 是否被覆盖 | 对应用例            |
| --- | --------------- | ----- | --------------- |
| EP1 | 存在且属于本人的记录      | 是     | TC01            |
| EP2 | 不存在的 ID         | 是     | TC09            |
| EP3 | id 为空           | 是     | TC03            |
| EP4 | 属于他人的记录         | 是     | TC10b           |
| EP5 | degree 1–6（若提供） | 是     | TC04（1）、TC05（6） |
| EP6 | degree 越界（若提供）  | 是     | TC06（0）、TC07（7） |
| EP7 | 部分字段更新          | 是     | TC02            |
| EP8 | 仅传 id           | 否     | 无               |


该接口等价类覆盖率为 87.5%，未覆盖项为仅传 id 不传任何其他字段的场景。

---

#### 7.1.6 GET /jobs


| EP   | 描述                    | 是否被覆盖 | 对应用例             |
| ---- | --------------------- | ----- | ---------------- |
| EP1  | page 正整数              | 是     | TC02             |
| EP2  | page ≤ 0（软边界纠正）       | 是     | TC03（0）、TC10（-3） |
| EP3  | page 不传               | 是     | TC01             |
| EP4  | limit 1–100           | 是     | TC02             |
| EP5  | limit ≤ 0（软边界纠正）      | 是     | TC04（0）          |
| EP6  | limit > 100（截断）       | 是     | TC05（101）        |
| EP7  | limit 不传              | 是     | TC01             |
| EP8  | employment 有效枚举       | 是     | TC09（FULL_TIME）  |
| EP9  | employment 非枚举值       | 是     | TC07（PART_TIME）  |
| EP10 | employment 不传         | 是     | TC01             |
| EP11 | filterSalaryMin ≤ Max | 是     | TC10             |
| EP12 | filterSalaryMin > Max | 否     | 无                |
| EP13 | jobName 有效关键词         | 是     | TC09             |
| EP14 | jobName 无匹配结果         | 否     | 无                |


该接口等价类覆盖率为 85.7%，未覆盖项为薪资逻辑冲突（最小薪资大于最大薪资）和 jobName 无匹配结果的场景。

---

#### 7.1.7 POST /jobs


| EP   | 描述                                | 是否被覆盖 | 对应用例               |
| ---- | --------------------------------- | ----- | ------------------ |
| EP1  | jobName 合法                        | 是     | TC01               |
| EP2  | jobName 为空                        | 是     | TC03               |
| EP3  | companyName 合法                    | 是     | TC01               |
| EP4  | companyName 为空                    | 否     | 无                  |
| EP5  | salaryType 1–3                    | 是     | TC01（salaryType=2） |
| EP6  | salaryType 越界                     | 是     | TC05（0）、TC06（4）    |
| EP7  | salaryType 为空                     | 是     | TC04               |
| EP8  | salaryMin ≤ Max                   | 是     | TC01               |
| EP9  | salaryMin > Max                   | 否     | 无                  |
| EP10 | salaryMin/Max 为空                  | 否     | 无                  |
| EP11 | 必填字段 description/location/link 合法 | 是     | TC01               |
| EP12 | 必填字段任一为空                          | 否     | 无（仅测了jobName缺失）    |
| EP13 | 可选字段均不传                           | 是     | TC01               |
| EP14 | 无 Token                           | 是     | TC09               |


该接口等价类覆盖率为 71.4%，未覆盖项为薪资逻辑冲突和部分必填字段缺失的场景。

---

#### 7.1.8 PUT /jobs


| EP  | 描述                | 是否被覆盖 | 对应用例    |
| --- | ----------------- | ----- | ------- |
| EP1 | 存在的岗位 ID          | 是     | TC01    |
| EP2 | 不存在的 ID           | 是     | TC06    |
| EP3 | id 为空             | 是     | TC03    |
| EP4 | id 非整数            | 是     | TC05    |
| EP5 | salaryType 1–3    | 是     | TC01    |
| EP6 | salaryType 越界     | 是     | TC08（4） |
| EP7 | 必填字段合法            | 是     | TC01    |
| EP8 | 必填字段为空（如 jobName） | 是     | TC04    |
| EP9 | 无 Token           | 是     | TC09    |


该接口等价类覆盖率为 100%。

### 7.2 BVA 覆盖率分析

从边界值分析覆盖情况来看，各接口在无效边界（越界值）上的覆盖相对充分，但在合法边界点（即区间端点的有效值）方面存在一定缺失，体现出 LLM 在边界值生成策略上的偏向性。


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


### 7.3 汇总统计

为从整体层面评估 LLM 测试用例生成能力，本文对所有接口的 EP 与 BVA 覆盖情况进行汇总统计，结果如下表所示。


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


总体来看，v3 版本在等价类覆盖方面达到 85.3%，在边界值覆盖方面达到 72.7%，表明 LLM 在生成结构性测试用例方面具备较强能力，但在边界精细化覆盖方面仍存在提升空间。

### 7.4 EP 覆盖率


| 接口                  | 基准 EP  | v1 覆盖  | v1 覆盖率    | v2 覆盖  | v2 覆盖率    | v3 覆盖  | v3 覆盖率    |
| ------------------- | ------ | ------ | --------- | ------ | --------- | ------ | --------- |
| POST /users         | 17     | 11     | 64.7%     | 12     | 70.6%     | 14     | 82.4%     |
| GET /users/{id}     | 6      | 4      | 66.7%     | 5      | 83.3%     | 5      | 83.3%     |
| PUT /users/password | 12     | 11     | 91.7%     | 11     | 91.7%     | 11     | 91.7%     |
| POST /educations    | 15     | 10     | 66.7%     | 11     | 73.3%     | 13    | 86.7%     |
| PUT /educations     | 8      | 6      | 75.0%     | 7      | 87.5%     | 7      | 87.5%     |
| GET /jobs           | 14     | 11     | 78.6%     | 12     | 85.7%     | 12     | 85.7%     |
| POST /jobs          | 14     | 9      | 64.3%     | 9      | 64.3%     | 10     | 71.4%     |
| PUT /jobs           | 9      | 8      | 88.9%     | 8      | 88.9%     | 9      | 100.0%    |
| **合计**              | **95** | **70** | **73.7%** | **75** | **78.9%** | **81** | **85.3%** |


### 7.5 BVA 覆盖率


| 接口                  | 基准 BV  | v1 覆盖  | v1 覆盖率    | v2 覆盖  | v2 覆盖率    | v3 覆盖  | v3 覆盖率    |
| ------------------- | ------ | ------ | --------- | ------ | --------- | ------ | --------- |
| POST /users         | 10     | 4      | 40.0%     | 4      | 40.0%     | 6      | 60.0%     |
| GET /users/{id}     | 4      | 3      | 75.0%     | 4      | 100.0%    | 4      | 100.0%    |
| PUT /users/password | 8      | 6      | 75.0%     | 6      | 75.0%     | 6      | 75.0%     |
| POST /educations    | 6      | 4      | 66.7%     | 4      | 66.7%     | 4      | 66.7%     |
| PUT /educations     | 4      | 4      | 100.0%    | 4      | 100.0%    | 4      | 100.0%    |
| GET /jobs           | 6      | 5      | 83.3%     | 5      | 83.3%     | 5      | 83.3%     |
| POST /jobs          | 4      | 2      | 50.0%     | 2      | 50.0%     | 2      | 50.0%     |
| PUT /jobs           | 2      | 1      | 50.0%     | 1      | 50.0%     | 1      | 50.0%     |
| **合计**              | **44** | **29** | **65.9%** | **30** | **68.2%** | **32** | **72.7%** |


### 7.6 输入组合测试分析

#### 7.6.1 方法说明

**输入组合测试**（Input Combination Testing）是一种针对多个输入参数组合的测试方法，旨在验证不同输入参数之间的交互和组合对系统行为的影响。该方法特别适用于参数多且独立可选的情况，尤其是存在多个参数交互场景时。在 **GET /jobs** 接口的测试中，由于其包含多个可选的查询参数（如 `page`, `limit`, `employment`, `filterSalaryMin`, `filterSalaryMax`, 等），并且这些参数的组合可能引发不同的查询结果，因此非常适合使用输入组合测试。

**适用性分析**：

- **参数多**：该接口涉及多个查询参数，每个参数都可以独立设置。
- **独立可选**：某些参数（如 `jobName`，`companyName`）可以单独设置，而不会影响其他参数的值。
- **参数交互**：例如，`filterSalaryMin` 和 `filterSalaryMax` 之间有依赖关系，`page` 和 `limit` 的调整会影响分页行为。

#### 7.6.2 结果分析

- 输入组合测试补充覆盖了以下 EP/BVA 没有覆盖的场景：
- **TC-14**：组合了 `filterSalaryMin > filterSalaryMax` 和 `industries` 参数，验证了 `min > max` 返回空列表的场景，这个场景是 **EP-20**（filterSalaryMin > filterSalaryMax）补充的。
- **TC-01**：验证了 `page` 和 `limit` 参数的修正与 `employment` + `jobName` 的过滤同时生效的场景，这个场景在 **EP-02**（page≤0→1）、**EP-06**（limit>100→100）和 **EP-17**（jobName inclusion）中有覆盖。

### 7.7 EP/BVA 缺失项分析

对照理论基准全集与 v3 生成结果，未覆盖项在等价类划分（EP）与边界值分析（BVA）两个维度上呈现出一些共性问题。本文将其归纳为以下五类典型问题：

#### 7.7.1 显式 null 场景覆盖不足（EP）

LLM 在构造缺失类测试用例时，倾向于使用“字段缺失（不传）”来表示异常输入，而较少生成“字段存在但值为 null”的情况。然而，在实际后端校验中，`null` 与“缺键”通常对应不同的校验路径（如 `@NotNull` 与 `@NotBlank`），因此需要分别设计测试用例。

该类缺失在多个接口中均有体现，例如：

- `/users` 接口中 password、email、verificationCode 的 null 等价类；
- `/users/password` 接口中 newPassword 为 null；
- `/educations` 接口中 degree 与 startDate 为 null。

#### 7.7.2 权限控制场景覆盖不足（EP）

在涉及鉴权的接口中，LLM 更倾向于生成“未认证（401）”相关测试用例，而对“已认证但无权限（403）”场景覆盖不足。

例如，在 `/users/{id}` 接口中，缺少“用户已登录但访问他人资源”的越权访问场景。这表明模型在未被显式引导时，对资源级权限控制的关注较弱。

#### 7.7.3 跨字段约束与业务逻辑场景缺失（EP）

涉及多个字段之间逻辑关系的测试场景（如数值约束、组合约束等）覆盖不充分，主要包括：

- 薪资区间逻辑冲突（如 `salaryMin > salaryMax`）；
- 多必填字段组合缺失；
- 查询条件逻辑冲突（如最小值大于最大值）；
- 查询结果为空（无匹配记录）等业务场景。

该现象表明，LLM 更擅长处理单字段约束，但对跨字段语义关系的建模能力相对有限。

#### 7.7.4 边界值生成偏向无效边界（BVA）

在边界值分析方面，模型更容易生成“越界值”（如 ±1 偏移），但对“合法边界值”（区间端点）以及“中间关键点”的覆盖相对不足，例如：

- 合法边界值（如长度恰为最小值或最大值）；
- 中间代表值（如枚举中间值）；
- 对称边界（如 +1/-1 成对情况未完整覆盖）。

该现象说明模型在边界值生成策略上存在明显的“负例偏向”。

#### 7.7.5 极简输入与部分更新场景缺失（EP）

对于支持部分更新的接口，LLM 较少生成“极简请求体”场景，例如仅包含标识字段（如 id）而不包含任何更新字段的请求。

该类用例对于验证接口在“空更新”或“无效更新”情况下的行为具有重要意义，但在生成结果中未被覆盖。

**小结**

EP 缺失多集中在 null 语义、403、跨字段业务规则与组合负例；BVA 缺失多集中在合法边界正向点与个别中间边界。对应的 Prompt 优化方向包括：在提示中显式要求区分「缺键 / null」、补充 403、列出跨字段约束，并点名合法边界上的正向用例。

---

## 8. 测试用例分析

本章在 v3 定稿用例集上，从 **Apifox 接口测试通过率**与**失败归因**两方面评估生成用例的可执行性与断言合理性。第 7 章已从 EP/BVA 角度讨论覆盖缺口；此处关注**请求发出后**的结果是否与预期一致。

### 8.1 测试用例执行结果

下表为 v3 版本各测试用例在 Apifox 上的执行情况统计、包含请求总数、成功、失败条数；


| 接口（功能） | API                 | 请求总数   | 成功数    | 失败数    |
| ------ | ------------------- | ------ | ------ | ------ |
| 查询用户信息 | GET /users/{id}     | 8      | 6      | 2      |
| 添加教育经历 | POST /educations    | 11     | 10     | 1      |
| 创建岗位   | POST /jobs          | 10     | 9      | 1      |
| 查岗位列表  | GET /jobs           | 11     | 8      | 3      |
| 更新岗位   | PUT /jobs           | 11     | 9      | 2      |
| 更新教育经历 | PUT /educations     | 12     | 9      | 3      |
| 重置密码   | PUT /users/password | 10     | 6      | 4      |
| 注册     | POST /users         | 10     | 7      | 3      |
| **合计** | —                   | **83** | **64** | **19** |


### 8.2 失败用例归因

经检查，19 条失败用例可归为三类：


| 类型                | 条数  | 说明                                                                                             |
| ----------------- | --- | ---------------------------------------------------------------------------------------------- |
| 后端服务器错误           | 4   | 被测环境或服务内部异常，**不视为**生成用例本身的设计错误。                                                                |
| 用例格式问题            | 1   | 请求体字段类型或结构与接口约定不符，属**生成质量**问题，可在导出或执行前做 schema 校验。                                             |
| 预期状态码 / 业务码与实测不一致 | 14  | 请求可送达且形态基本合理，但**断言中的 HTTP 状态码或业务码**与接口实际返回不一致；常见情形是对错误场景应返回 **400** 还是 **404**、以及业务错误码分档的理解偏差。 |


**总结**：多数失败并非「请求发不出去」，而是**预期结果标注**与运行环境真实返回存在偏差；这与第 7 章讨论的隐含规则、跨字段逻辑未完全写入用例预期有关，需在人工复核或迭代提示中收紧「期望结果」条款。

### 8.3 本章小结

v3 用例在 **请求构造与可执行性**上整体可用，**77.1%** 的原始通过率与 **81.0%** 的有效通过率表明：LLM 适合作为黑盒用例的**初稿生成器**，但**业务状态码与错误码断言**仍需结合接口文档与实测逐条校对。后续可优先针对低通过率接口（如重置密码）增加「错误码—场景」对照说明，并约束输出格式以降低格式类失败。

---

## 9. 状态迁移测试

### 9.1 状态定义

本系统存在明显的用户状态流转，共定义以下五个状态：


| 状态          | 描述                              |
| ----------- | ------------------------------- |
| 未注册         | 用户尚未在系统中创建账号                    |
| 已注册·未登录     | 账号已存在但未获取有效 Token               |
| 已登录·Token有效 | 已登录且持有未过期的 JWT Token，可访问所有需认证接口 |
| 已登录·Token失效 | Token 已过期，需重新登录才能访问受保护资源        |
| 密码已重置·仍登录   | 在已登录状态下完成密码重置，当前 Token 仍有效      |


### 9.2 状态转移图

状态转移图见附图，完整展示了五个状态之间的全部迁移路径，包括正向迁移（绿色箭头）、自环迁移（橙色箭头）和非法操作被拒绝（红色箭头）。

### 9.3 Prompt 设计

使用以下 Prompt 生成状态迁移测试用例：

```
You are a software testing expert performing black-box testing.
Given the following system overview and state definitions:
{Input}
Generate the result in a structured testing report format with two sections:

State Transition Diagram
Table columns: Current State | Operation / API | Condition | Next State | Expected Result
State Transition Test Cases
Table columns: Test Case ID | Current State | Operation / API | Input Summary | Expected Result | Test Type

Requirements:

Define all system states clearly (e.g., Unregistered, Registered-NotLoggedIn,
LoggedIn-TokenValid, LoggedIn-TokenExpired)
Cover three types of transitions:

Valid transitions: operations that successfully change the system state
Invalid transitions: operations performed in a wrong state that should be rejected
Self-loop transitions: operations that fail and leave the system in the same state


Each test case must correspond to exactly ONE state transition
Include both authentication errors (401) and authorization errors (403)
Generate exactly 10 high-quality test cases (no duplicates)
Use ONLY the provided requirements — do NOT assume extra rules
Keep output concise, table-based, and suitable for a report
```

### 9.4 测试用例

#### 9.4.1 第一类：正向迁移（操作成功，状态发生变化）


| 用例   | 当前状态        | 操作                  | 期望结果         | 期望新状态        |
| ---- | ----------- | ------------------- | ------------ | ------------ |
| ST01 | 未注册         | POST /users（合法参数）   | 200          | 已注册·未登录      |
| ST02 | 已注册·未登录     | POST /users/login   | 200，返回 Token | 已登录·Token有效  |
| ST03 | 已登录·Token有效 | PUT /users/password | 200          | 密码已重置·仍登录    |
| ST04 | 已登录·Token有效 | POST /educations    | 200          | 已登录（教育经历已添加） |


#### 9.4.2 第二类：非法迁移（在错误状态下操作被拒绝）


| 用例   | 当前状态        | 操作                        | 期望结果         |
| ---- | ----------- | ------------------------- | ------------ |
| ST05 | 未注册         | POST /users/login         | 401，登录失败     |
| ST06 | 已注册·未登录     | POST /educations（无 Token） | 401，未认证      |
| ST07 | 已登录·Token失效 | GET /users/{id}           | 401，Token 无效 |
| ST08 | 已登录·Token有效 | 访问他人教育经历                  | 403，无权限      |


#### 9.4.3 第三类：自环迁移（操作失败，状态不变）


| 用例   | 当前状态    | 操作                      | 期望结果           |
| ---- | ------- | ----------------------- | -------------- |
| ST09 | 已注册·未登录 | 再次 POST /users（同邮箱）     | 409，邮箱已存在，状态不变 |
| ST10 | 已注册·未登录 | POST /users/login（密码错误） | 401，登录失败，状态不变  |


### 9.5 分析

状态迁移测试覆盖了 EP/BVA 无法覆盖的**时序依赖场景**。从测试结果来看，系统对正向迁移路径（ST01–ST04）的处理逻辑正确；对非法迁移（ST05–ST08）能准确返回 401/403，说明认证与授权机制工作正常；自环迁移（ST09–ST10）中系统能正确拒绝操作且不改变状态，业务规则执行符合预期。

值得注意的是，ST07（Token 失效后访问受保护接口返回 401）与 ST08（已登录但越权操作返回 403）在 EP/BVA 测试中已作为独立用例出现，状态迁移测试将其纳入完整的状态流转上下文中，进一步验证了这两个场景在真实使用序列下的行为一致性。

---

## 10. LLM 能力分析

本章节基于前述实验结果，从对比分析、局限性归纳及改进策略三个方面，对大语言模型在黑盒测试用例生成中的能力进行系统评估。

### 10.1 与传统测试设计方法的对比

基于本实验中 8 个接口的实际测试设计过程，从效率、覆盖完整性、格式一致性等多个维度，对 LLM 辅助测试设计方法与传统人工测试设计方法进行对比分析，结果如下表所示。


| **对比维度**   | **传统人工设计**                    | **LLM 辅助设计（本实验）**                            |
| ---------- | ----------------------------- | -------------------------------------------- |
| **效率**     | 8个接口的 EP/BVA/用例设计约需数个小时       | v1 生成约 10 秒/接口，加上 3 轮迭代和人工复核，总计约 2-3 小时      |
| **覆盖完整性**  | 依赖设计者经验，易遗漏边界（如 null 场景、越权场景） | 基础覆盖较全（v1 即覆盖 20 个 EP），但跨字段逻辑、null 语义等仍需人工补充 |
| **格式一致性**  | 因人而异，需统一模板                    | 提示词约束下格式统一，便于直接执行                            |
| **错误场景覆盖** | 需逐一枚举，容易漏掉组合错误                | 能系统枚举单参数错误，但合并用例倾向需人工拆分                      |
| **可维护性**   | 需求变更时需人工逐条修改                  | 修改提示词后重新生成，更新成本低                             |
| **依赖技能**   | 需熟悉测试设计方法 + 业务规则              | 需会写提示词 + 能判断输出质量                             |


**方法优缺点对比**


| **类型**     | **优点**                    | **缺点**                      |
| ---------- | ------------------------- | --------------------------- |
| **LLM 辅助** | 速度快、基础覆盖全、格式统一、需求变更时更新成本低 | 输出不稳定、需多轮迭代、易合并用例、对隐含规则覆盖不足 |
| **人工设计**   | 能处理复杂业务逻辑、跨字段约束、细粒度权限场景   | 耗时长、易遗漏边界、格式不统一             |


综合对比结果可以看出：

- 在**测试设计效率**方面，LLM 显著优于传统人工方法，能够在极短时间内生成初始测试用例集合；
- 在**基础覆盖能力**方面，LLM 能够较为系统地覆盖单参数约束，但在复杂业务规则（如跨字段逻辑、权限控制）方面仍依赖人工补充；
- 在**工程化能力**方面，LLM 生成结果具有良好的格式一致性与可复用性，有利于自动化测试执行；
- 在**稳定性与精细度**方面，人工设计在复杂场景处理上仍具有不可替代的优势。

因此，LLM 更适合作为测试设计的“辅助工具”而非完全替代人工的方法。

### 10.2 实践中遇到的 AI 局限性

基于本实验 v1–v3 多轮迭代过程及覆盖率分析结果，可以归纳出 LLM 在测试用例生成中的若干典型局限性。


| **局限性**        | **具体表现（来自实验数据）**                                                                                                                                                           | **影响**                 |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------- |
| **倾向于合并用例**    | v2 的 TC10 将“existing username → 409/2001”、“existing email → 409/2002”、“invalid/expired verificationCode → 400/2003”三个业务错误合并成一条用例，只写“corresponding business error returned” | 无法区分具体错误码，不利于缺陷定位      |
| **遗漏 null 语义** | 根据 8.1 EP 覆盖率分析，`POST /users` 未覆盖 EP8（password 为空/null）、EP12（email 为空/null）、EP15（verificationCode 为空/null）                                                                 | 覆盖缺口，null 与缺失字段的触发路径不同 |
| **遗漏越权场景**     | 根据 8.1 EP 覆盖率分析，`GET /users/{id}` 未覆盖 EP6（越权查他人信息 → 403）                                                                                                                   | 安全测试覆盖不足               |
| **遗漏跨字段逻辑**    | 根据 8.1 EP 覆盖率分析，`POST /jobs` 未覆盖 EP9（salaryMin > salaryMax）                                                                                                                | 业务规则覆盖不足               |


### 10.3 改进方法

针对上述局限性，本文从提示词设计与测试流程两个层面提出改进策略。

#### 10.3.1 提示词优化策略

为提升 LLM 输出质量，可通过增强提示词约束来引导模型生成更加结构化与完整的测试用例。


| **局限性**    | **提示词改进方式**                                                   |
| ---------- | ------------------------------------------------------------- |
| 合并用例       | 增加约束：`每个用例必须清晰对应一条规则或边界`                                      |
| 遗漏 null 语义 | 明确要求：`区分“字段缺失（不传该字段）”和“字段存在但值为 null”两类场景`                     |
| 遗漏越权场景     | 明确要求：`包含 403 无权限场景（已登录但操作他人资源）`                               |
| 遗漏跨字段逻辑    | 明确要求：`包含跨字段逻辑冲突（如 salaryMin > salaryMax、endDate < startDate）` |


#### 10.3.2 流程优化策略

为进一步提升测试用例生成质量，本文采用多轮迭代与人工复核相结合的流程优化策略，具体包括：

- **迭代生成（v1 → v2 → v3）**
  - v1：快速生成初始测试用例，用于识别主要覆盖缺口；
  - v2：补充格式与结构约束，修正明显遗漏；
  - v3：引入细粒度约束（如 null 区分、用例拆分等），进一步提升覆盖完整性；
- 人工复核：在每轮生成后，人工重点检查以下高频遗漏场景：
  - `null` 语义相关测试；
  - 权限控制（403）场景；
  - 跨字段逻辑冲突；
- **提示词模板沉淀：**将高频遗漏项与有效约束规则固化为提示词模板，并按接口类型（如 CRUD、分页查询、认证流程）进行分类复用，从而提升后续测试设计的一致性与效率。

### 10.4 小结

LLM 在黑盒测试用例生成任务中表现出显著的效率优势与较强的基础覆盖能力，尤其适用于测试用例初稿的快速生成。然而，其在复杂业务规则建模、隐含约束识别以及边界值系统性覆盖方面仍存在明显不足。

通过引入“迭代式提示词优化 + 人工复核补充”的混合方法，可以在保留 LLM 高效率优势的同时，有效弥补其覆盖缺口，从而形成一种可行的测试设计范式。在本实验中，最终 v3 测试用例在等价类划分方面达到 85.3% 的覆盖率，在边界值分析方面达到 72.7%，验证了该方法在实际工程中的可行性与应用价值。

---

## 11. 总结

本实验围绕 8 个 REST API 接口，建立了“接口文档输入—LLM 生成—多轮迭代优化—Apifox 执行验证—覆盖与失败归因分析”的完整黑盒测试流程。结果表明，LLM 在测试用例初稿生成方面具备明显效率优势，能够快速产出结构化内容并支持模板化复用。经过 v1–v3 迭代后，最终用例集达到 EP 覆盖率 85.3%、BVA 覆盖率 72.7%，总体执行通过率 77.1%，排除后端异常后的有效通过率为 81.0%。

从质量角度看，LLM 对显式规则（必填、格式、枚举、常规边界）的覆盖较好，但在隐含规则与复杂约束（null 语义区分、403 越权、跨字段逻辑、合法边界正向值）上仍存在稳定性不足和系统性漏测。该结论与第 7 章缺失项分析及第 8 章失败归因结果一致，说明仅依赖单轮生成难以满足工程级测试的完整性要求。

因此，本文建议将 LLM 定位为“高效率测试初稿生成器”，并采用“提示词约束强化 + 迭代生成 + 人工复核校正”的混合流程：先由模型快速生成初稿，再由测试人员补齐高风险场景与断言细节。该方法能够在保证效率的同时提升结果可用性与过程可追踪性。

后续可考虑从以下方向继续改进流程：优化提示词，减少对人工调整的依赖；加强对跨字段约束等复杂场景的生成能力；将测试用例生成与自动执行结合，减少人工参与；引入更多评估指标，提高结果分析的全面性。

## 附录

### 附录 A 和 LLM 的对话记录

详见文件 

### 附录 B EP_BVA全集

详见文件