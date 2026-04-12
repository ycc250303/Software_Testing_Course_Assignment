# 作业 1（中文翻译）

## 1. 要求

本次作业要求学生使用 AI 方法（例如 LLM）设计并实现（或增强）一种测试技术。学生可选择以下方向之一：

- 静态测试（Static Testing）
- 黑盒动态测试（Black-box Dynamic Testing）
- 白盒动态测试（White-box Dynamic Testing）

说明：  
静态代码分析工具列表见原文对应页面；黑盒测试技术包括等价类划分（EP）、边界值分析（BVA）、输入组合测试、状态迁移测试模型生成、判定表测试；白盒测试可度量语句覆盖率、分支覆盖率、条件覆盖率、路径覆盖率、d-u 覆盖率等。

作业要求将成果实现为一个工具，该工具可接收两类输入之一：

1. 系统需求说明（requirements）
2. 被测代码库（testing codebase）

工具应能够分析上述输入（可为完整代码库或部分模块）并生成测试用例。

## 2. 提交产物（Submission Artifact）

1. 输入：需求文档 / 项目代码库  
2. 工具产物：使用的提示词（prompts）、使用的模型、模型生成的代码  
3. 生成输出：  
   - 静态分析：报告告警（reported alarms）  
   - 黑盒与白盒分析：测试用例（test cases）  
4. 实验分析（准确率、覆盖率、泛化能力等）  
5. 项目报告：  
   - 与传统非 AI 技术的对比、优缺点  
   - 分析报告：实践中遇到 AI 的局限性及改进方式  
   - 总结  

## 3. 评分标准（Assessment Criteria）

- 概念理解：10%
- 设计与实现连贯性：20%
- 覆盖度与有效性/实用性：40%
- 深度分析（泛化能力展示、推理等）：20%
- 展示表现：10%

---

## 4. 展示（Presentation）

每组有 15 分钟使用英语完成项目展示，需覆盖上述全部内容。随后进行问答环节。评审将根据提交文档、展示内容以及学生应掌握的软件测试基础提出问题。

组内贡献默认按平均分配。若需调整，必须单独申请并由全体成员签字同意。

## 5. 提交（Submission）

每组须在展示日前 1 天通过邮件向助教提交以下材料：

1. **提交产物（Submission Artifact）**：包含上述全部要求内容（封面需包含组号、全体成员姓名、学号）。  
2. **最终展示 PPT**：第一页需包含组号、全体成员姓名、学号。  

报告与 PPT 须提交为 PDF 格式；测试脚本须打包为压缩文件提交。

### 提交与展示时间安排

- 提交截止：第 8 周（4 月 20 日，周一）17:00 前  
- 展示时间：第 8–9 周，周二/周四，10:00–11:35  

---

## 示例提交 Ex1

**标题：** 基于 LLM 的多商品智能售货机动态黑盒测试

### 1) 输入

**系统概述：**  
被测系统是部署在公共区域（如地铁站）的智能售货机。测试者无法看到内部软件、硬件控制逻辑和数据库。测试仅基于可观察到的外部行为，并依据给定需求执行。

**功能需求：**

- **R1. 商品选择**：售货机提供三类商品：  
  - 饮料（价格范围：$1.50–$3.00）  
  - 零食（价格范围：$2.00–$4.50）  
  - 热食（价格范围：$5.00–$10.00）
- **R2. 支付方式**：支持多种支付方式：  
  1) 硬币：$0.10、$0.25、$0.50、$1.00  
  2) 纸币：$5.00、$10.00
- **R3. 支付约束**：投币总额必须大于等于商品价格。机器最多只找零 $5.00；若交易需要找零超过 $5.00，则拒绝交易。
- **R4. 库存约束**：商品在支付过程中可能售罄。

### 2) 工具产物

- 使用模型：GPT-4o  
- 使用提示词（示例）：

```text
You are a software testing assistant. Given the following system overview and requirement, identify:
1) Input variables
2) Equivalence partitions (valid and invalid)
3) Boundary values
4) Concrete test cases
Requirement: {requirement_text}
```

### 3) 生成输出

**等价类划分：**

| ID | 描述 | 结果 |
|---|---|---|
| EP1 | 投入金额 < 商品价格 | 有效（原文如此） |
| EP2 | 投入金额 > 商品价格 | 无效（原文如此） |
| … | … | … |

**边界值分析：**

| 边界项 | 取值 |
|---|---|
| 商品价格 | $1.4, $1.5, $1.6 |
| 支付总额 | $0, $1.4, $15.1 |
| … | … |

**样例测试用例：**

| 测试用例 | 场景 | 期望结果 |
|---|---|---|
| TC1 | 零食 $3.00，支付 $8.50 | 拒绝（找零 > $5） |
| TC2 | 零食 $3.00，支付 $3.50 | 支付成功，找零 $0.50 |
| … | … | … |

### 4) 实验分析

1. EP/BVA 与测试用例覆盖情况  
2. 缺失项分析  
3. 通过优化提示词提升覆盖率、准确率与泛化能力  

### 5) 项目报告

1. 与传统非 AI 技术对比、优缺点  
2. 分析报告：AI 局限性及工具改进方式  
3. 总结  

---

## 示例提交 Ex2

**标题：** 基于 LLM 的静态分析

### 1) 输入

**系统概述：**  
Axios 是一个基于 Promise 的网络请求库，可运行于 Node.js 与浏览器。该版本在保留原有使用模式与功能的前提下，基于 Axios v1.3.4 进行了适配，使其可兼容 OpenHarmony。

- HTTP 请求
- Promise API
- 请求与响应拦截器
- 请求与响应数据转换
- JSON 数据自动转换

Axios 源码地址（原文）：  
`https://ohpm.openharmony.cn/#/cn/detail/@ohos%2Faxios`

### 2) 工具产物

**示例提示词：**

```text
You are a static code analyzer. Analyze the following {language} code and detect potential issues.
Identify the following:
1. Syntax errors
2. Security vulnerabilities
3. Deprecated or incompatible API usage
4. Potential runtime errors
5. Code quality issues
Return the results in structured JSON format:
{
  "issues": [
    {
      "line": <line_number>,
      "type": "<issue_type>",
      "description": "<detailed description>",
      "severity": "<low/medium/high>"
    }
  ]
}
Code:
{source_code}
```

### 3) 生成输出

**问题 1（示例）：**

```json
[{
  "line": 4,
  "type": "Resource Management",
  "description": "File opened using 'open' but not managed with a context manager. If an exception occurs, the file may remain open.",
  "severity": "medium",
  "recommendation": "Use 'with open(filename, 'r') as f:' to automatically close the file.",
  "category": "Code Quality",
  "reference": "https://docs.python.org/3/tutorial/inputoutput.html#reading-and-writing-files"
}]
```

测试用例（概念验证）：

### 4) 实验分析

1. 误报（False Alarms）分析  
2. 通过优化提示词提升准确率  
3. 尝试更多项目提升泛化能力  
4. 缺陷上报与开发者验证  

### 5) 项目报告

1. 与传统非 AI 技术对比、优缺点  
2. 分析报告：AI 局限性与改进方法  
3. 总结  

---

## 示例提交 Ex3

**标题：** 基于 LLM 的白盒测试

### 1) 输入

**系统概述：**  
Axios 是一个基于 Promise 的网络请求库，可运行于 Node.js 与浏览器。该版本在保留原有使用模式与功能的前提下，基于 Axios v1.3.4 进行了适配，使其可兼容 OpenHarmony。

- HTTP 请求
- Promise API
- 请求与响应拦截器
- 请求与响应数据转换
- JSON 数据自动转换

Axios 源码地址（原文）：  
`https://ohpm.openharmony.cn/#/cn/detail/@ohos%2Faxios`

### 2) 工具产物

**提示词：**

```text
You are an expert software tester and white-box testing assistant.
Your task is to analyze the following {language} function/module and generate a set of test cases that achieve full statement coverage.
Please do the following:
1. Identify all executable statements in the code.
2. For each statement, generate test input values that will execute it at least once.
3. Output the test cases in a structured JSON format suitable for automated testing.
Structured JSON format:
{
  "function": "<function_name>",
  "test_cases": [
    {
      "input": { "<parameter_name>": <value>, ... },
      "expected_output": "<expected output or behavior>",
      "covered_statements": [<line_numbers>],
      "notes": "<optional explanation>"
    }
  ]
}
Code to analyze:
{code_snippet}
Notes:
- Include edge cases if necessary to cover all branches of conditional statements.
- If a statement is unreachable due to a logic error, mark it as "unreachable".
- Provide explanations for how each test case achieves statement coverage.
```

### 3) 生成输出

- 已测函数（Tested Function）  
- 测试用例：  
  - TestCase1：删除指定文件  
  - TestCase2：异常处理  

### 4) 实验分析

1. 误报分析  
2. 通过优化提示词提升准确率  
3. 尝试更多项目提升泛化能力  
4. 缺陷上报与开发者验证  

### 5) 项目报告

1. 与传统非 AI 技术对比、优缺点  
2. 分析报告：AI 局限性与改进方法  
3. 总结  

---

> 说明：原 PDF 中存在少量 OCR 字符错误（如 `Tes6ng`、`hdps`、`exis:ng` 等），本译文在不改变原意的前提下做了规范化处理。
