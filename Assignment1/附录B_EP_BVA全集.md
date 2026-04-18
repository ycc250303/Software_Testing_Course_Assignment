# 附录B_EP_BVA全集

## 附录 B 人工编写的 EP/BVA 全集

为保证覆盖率统计的可度量性与可复现性，本文首先基于接口文档人工构建理论测试基准全集，并以此作为 LLM 生成结果的对照标准。其中，EP 基准总数共 95 个，BV 基准总数共 44 个。

### B.1 POST /users（用户注册）


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

### B.2 GET /users/ {id}（根据 ID 查用户）

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

### B.3 PUT /users/password（重置密码）

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

### B.4 POST /educations（添加教育经历）

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

### B.5 PUT /educations（更新教育经历）

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

### B.6 GET /jobs（查询岗位列表）

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

### B.7 POST /jobs（创建岗位）

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

### B.8 PUT /jobs（更新岗位）

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
