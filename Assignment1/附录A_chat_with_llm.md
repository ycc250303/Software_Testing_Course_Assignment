### 1.POST /users

**Input**

```text
System Overview:
The system under test is a RESTful user management API. The internal implementation is not visible. Testing is performed based on API specifications.

Functional Requirements:
R1. User Registration:
The system provides an API endpoint POST /users to register a new user.

R2. Input Constraints:
- username: required, length 3–50 characters
- password: required, length 6–20 characters
- email: required, valid email format, maximum length 100
- verificationCode: required, exactly 6 digits

R3. Business Rules:
- Username must be unique
- Email must be unique
- Verification code must be valid and not expired

R4. Error Handling:
- Invalid parameters -> HTTP 400, code 1001
- Username already exists -> HTTP 400, code 2001
- Email already exists -> HTTP 400, code 2002
- Invalid verification code -> HTTP 400, code 2003

R5. Success Response:
- HTTP 200
- code = 0
```

#### v1

**Prompt**

```text
You are a software testing expert.

Given the following system overview and requirements:
{Input}

Generate the result in a report format with three sections:
1.Equivalence Partitioning
Table columns: ID | Description | Outcome
2.Boundary Value Analysis
Table columns: Field | Boundary Values
3.Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result
Generate exactly 10 test cases.
```

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                             | Outcome                        |
| ---- | --------------------------------------------------------------------------------------- | ------------------------------ |
| EP1  | All inputs valid; username and email are new; verificationCode is valid and not expired | Valid                          |
| EP2  | username missing                                                                        | Invalid -> HTTP 400, code 1001 |
| EP3  | username length < 3                                                                     | Invalid -> HTTP 400, code 1001 |
| EP4  | username length 3-50                                                                    | Valid                          |
| EP5  | username length > 50                                                                    | Invalid -> HTTP 400, code 1001 |
| EP6  | password missing                                                                        | Invalid -> HTTP 400, code 1001 |
| EP7  | password length < 6                                                                     | Invalid -> HTTP 400, code 1001 |
| EP8  | password length 6-20                                                                    | Valid                          |
| EP9  | password length > 20                                                                    | Invalid -> HTTP 400, code 1001 |
| EP10 | email missing                                                                           | Invalid -> HTTP 400, code 1001 |
| EP11 | email format valid and length <= 100                                                    | Valid                          |
| EP12 | email format invalid                                                                    | Invalid -> HTTP 400, code 1001 |
| EP13 | email length > 100                                                                      | Invalid -> HTTP 400, code 1001 |
| EP14 | verificationCode missing                                                                | Invalid -> HTTP 400, code 1001 |
| EP15 | verificationCode is exactly 6 digits and valid/not expired                              | Valid                          |
| EP16 | verificationCode not 6 digits or contains non-digit characters                          | Invalid -> HTTP 400, code 1001 |
| EP17 | username already exists                                                                 | Invalid -> HTTP 400, code 2001 |
| EP18 | email already exists                                                                    | Invalid -> HTTP 400, code 2002 |
| EP19 | verificationCode is 6 digits but invalid                                                | Invalid -> HTTP 400, code 2003 |
| EP20 | verificationCode is expired                                                             | Invalid -> HTTP 400, code 2003 |


**2) Boundary Value Analysis**


| Field                               | Boundary Values                                  |
| ----------------------------------- | ------------------------------------------------ |
| username (length 3-50)              | 2, 3, 4, 49, 50, 51                              |
| password (length 6-20)              | 5, 6, 7, 19, 20, 21                              |
| email (max 100)                     | 99, 100, 101 characters                          |
| verificationCode (exactly 6 digits) | 5 digits, 6 digits, 7 digits                     |
| verificationCode content            | 6 numeric digits, 6 chars with non-digit present |
| required fields                     | missing, present                                 |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                                      | Expected Result       |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| TC01         | Register with valid username (length 10), valid password (length 8), valid email (<100 chars), and valid non-expired 6-digit verificationCode | HTTP 200, code = 0    |
| TC02         | Register with username length = 2                                                                                                             | HTTP 400, code = 1001 |
| TC03         | Register with username length = 51                                                                                                            | HTTP 400, code = 1001 |
| TC04         | Register with password length = 5                                                                                                             | HTTP 400, code = 1001 |
| TC05         | Register with password length = 21                                                                                                            | HTTP 400, code = 1001 |
| TC06         | Register with email length = 101 characters                                                                                                   | HTTP 400, code = 1001 |
| TC07         | Register with invalid email format (for example, missing @)                                                                                   | HTTP 400, code = 1001 |
| TC08         | Register with verificationCode containing 5 digits                                                                                            | HTTP 400, code = 1001 |
| TC09         | Register with an existing username and a new email                                                                                            | HTTP 400, code = 2001 |
| TC10         | Register with a new username and email, but verificationCode is expired or invalid                                                            | HTTP 400, code = 2003 |


#### v2

**Prompt**

```text
You are a software testing expert performing black-box testing.
Given the following system overview and requirements:
{Input}
Generate the result in a structured testing report format with three sections:
1.Equivalence Partitioning
Table columns: ID | Description | Outcome
Include both valid and invalid equivalence classes.
2.Boundary Value Analysis
Table columns: Field | Boundary Values
Include boundary values for all input fields (username, password, email, verificationCode).
3.Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result
Requirements:
Cover valid inputs, missing fields, boundary values, invalid formats, and business errors
Generate exactly 10 test cases
Keep output concise and structured
```

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                                | Outcome                        |
| ---- | ------------------------------------------------------------------------------------------ | ------------------------------ |
| EP01 | All fields valid; username and email are unique; verificationCode is valid and not expired | Valid                          |
| EP02 | username missing                                                                           | Invalid -> HTTP 400, code 1001 |
| EP03 | username length < 3                                                                        | Invalid -> HTTP 400, code 1001 |
| EP04 | username length 3-50                                                                       | Valid                          |
| EP05 | username length > 50                                                                       | Invalid -> HTTP 400, code 1001 |
| EP06 | password missing                                                                           | Invalid -> HTTP 400, code 1001 |
| EP07 | password length < 6                                                                        | Invalid -> HTTP 400, code 1001 |
| EP08 | password length 6-20                                                                       | Valid                          |
| EP09 | password length > 20                                                                       | Invalid -> HTTP 400, code 1001 |
| EP10 | email missing                                                                              | Invalid -> HTTP 400, code 1001 |
| EP11 | email format valid and length <= 100                                                       | Valid                          |
| EP12 | email format invalid                                                                       | Invalid -> HTTP 400, code 1001 |
| EP13 | email length > 100                                                                         | Invalid -> HTTP 400, code 1001 |
| EP14 | verificationCode missing                                                                   | Invalid -> HTTP 400, code 1001 |
| EP15 | verificationCode is exactly 6 digits                                                       | Valid format class             |
| EP16 | verificationCode not exactly 6 digits                                                      | Invalid -> HTTP 400, code 1001 |
| EP17 | verificationCode contains non-digit characters                                             | Invalid -> HTTP 400, code 1001 |
| EP18 | username already exists                                                                    | Invalid -> HTTP 400, code 2001 |
| EP19 | email already exists                                                                       | Invalid -> HTTP 400, code 2002 |
| EP20 | verificationCode invalid or expired                                                        | Invalid -> HTTP 400, code 2003 |


**2) Boundary Value Analysis**


| Field            | Boundary Values                                               |
| ---------------- | ------------------------------------------------------------- |
| username         | 2, 3, 4, 49, 50, 51 characters                                |
| password         | 5, 6, 7, 19, 20, 21 characters                                |
| email            | 0/missing, valid typical length, 99, 100, 101 characters      |
| verificationCode | missing, 5 digits, 6 digits, 7 digits, 6 chars with non-digit |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                                                                                               | Expected Result                       |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------- |
| TC01         | Register with valid username, valid password, valid email, and valid non-expired 6-digit verificationCode                                                                                              | HTTP 200, code = 0                    |
| TC02         | Register with username missing                                                                                                                                                                         | HTTP 400, code = 1001                 |
| TC03         | Register with username length = 2                                                                                                                                                                      | HTTP 400, code = 1001                 |
| TC04         | Register with username length = 51                                                                                                                                                                     | HTTP 400, code = 1001                 |
| TC05         | Register with password length = 5                                                                                                                                                                      | HTTP 400, code = 1001                 |
| TC06         | Register with password length = 21                                                                                                                                                                     | HTTP 400, code = 1001                 |
| TC07         | Register with email in invalid format                                                                                                                                                                  | HTTP 400, code = 1001                 |
| TC08         | Register with email length = 101 characters                                                                                                                                                            | HTTP 400, code = 1001                 |
| TC09         | Register with verificationCode = 5 digits, or contains non-digit characters                                                                                                                            | HTTP 400, code = 1001                 |
| TC10         | Register with otherwise valid input but trigger business errors: existing username -> HTTP 400/code 2001; existing email -> HTTP 400/code 2002; invalid/expired verificationCode -> HTTP 400/code 2003 | Corresponding business error returned |


#### v3

**Prompt**

```text
You are a software testing expert performing black-box testing.
Given the following system overview and requirements:
{Input}
Generate the result in a formal software testing report format with three sections:
1. Equivalence Partitioning
   Table columns: ID | Description | Outcome
* Clearly distinguish valid and invalid classes
* Cover constraints: required fields, length limits, format validation, and uniqueness
2. Boundary Value Analysis
   Table columns: Field | Boundary Values
* Include meaningful boundary values for each field
* username: length boundaries
* password: length boundaries
* email: length and format boundaries
* verificationCode: digit length and format boundaries
3. Sample Test Cases
   Table columns: Test Case ID | Scenario | Expected Result
Requirements:
* Use ONLY the provided requirements
* Cover: normal case, missing fields, boundary values, invalid formats, duplicate username/email, and invalid verification code
* Generate exactly 10 high-quality test cases (no duplicates)
* Each test case must correspond to a rule or boundary condition
* Keep output concise, table-based, and suitable for a report
```

**Output**

**1) Equivalence Partitioning**


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
| EP16 | verificationCode length ≠ 6                                                                                     | Invalid -> HTTP 400, code 1001 |
| EP17 | verificationCode contains non-digit characters                                                                  | Invalid -> HTTP 400, code 1001 |
| EP18 | username already exists                                                                                         | Invalid -> HTTP 400, code 2001 |
| EP19 | email already exists                                                                                            | Invalid -> HTTP 400, code 2002 |
| EP20 | verificationCode invalid or expired                                                                             | Invalid -> HTTP 400, code 2003 |


**2) Boundary Value Analysis**


| Field            | Boundary Values                                                    |
| ---------------- | ------------------------------------------------------------------ |
| username         | 2, 3, 4, 49, 50, 51 characters                                     |
| password         | 5, 6, 7, 19, 20, 21 characters                                     |
| email            | missing, valid format, invalid format, 99, 100, 101 characters     |
| verificationCode | missing, 5 digits, 6 digits, 7 digits, 6 characters with non-digit |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                      | Expected Result       |
| ------------ | --------------------------------------------------------------------------------------------- | --------------------- |
| TC01         | Register with valid username, password, email, and valid non-expired 6-digit verificationCode | HTTP 200, code = 0    |
| TC02         | Register with username missing                                                                | HTTP 400, code = 1001 |
| TC03         | Register with username length = 2                                                             | HTTP 400, code = 1001 |
| TC04         | Register with username length = 51                                                            | HTTP 400, code = 1001 |
| TC05         | Register with password length = 5                                                             | HTTP 400, code = 1001 |
| TC06         | Register with password length = 21                                                            | HTTP 400, code = 1001 |
| TC07         | Register with email in invalid format                                                         | HTTP 400, code = 1001 |
| TC08         | Register with verificationCode containing 6 characters with a non-digit                       | HTTP 400, code = 1001 |
| TC09         | Register with an existing username and otherwise valid inputs                                 | HTTP 400, code = 2001 |
| TC10         | Register with a new username and email, but an invalid or expired verificationCode            | HTTP 400, code = 2003 |


### 2.GET /users/ {id}

**Input**

```text
System Overview:
The system under test is a RESTful user management API. The internal implementation is not visible. Testing is performed based on API specifications.

Functional Requirements:

R1. Get User by ID:
The system provides an API endpoint GET /users/{id} to retrieve user information by user ID.

R2. Input Constraints:

id: required path parameter, integer
R3. Business Rules:

The requested user ID must exist
R4. Error Handling:

User does not exist → HTTP 404, code 2006
Invalid parameter or type error → HTTP 400, code 1001
Unauthorized access → HTTP 401, code 401
R5. Authentication:

This endpoint requires bearer token authentication
R6. Success Response:

HTTP 200
code = 0
data      contains user information: id, username, email, name, phone, website
```

#### v1

**Prompt**

```text
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

**Output**

**1) Equivalence Partitioning**


| ID  | Description                                                            | Outcome                                                                                                                  |
| --- | ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| EP1 | Valid integer id, user exists, valid bearer token provided             | HTTP 200, code=0, data contains id, username, email, name, phone, website                                                |
| EP2 | Valid integer id, but user does not exist, valid bearer token provided | HTTP 404, code=2006                                                                                                      |
| EP3 | Missing bearer token                                                   | HTTP 401, code=401                                                                                                       |
| EP4 | Invalid bearer token / unauthorized token                              | HTTP 401, code=401                                                                                                       |
| EP5 | id is non-integer type, such as string or alphabetic value             | HTTP 400, code=1001                                                                                                      |
| EP6 | id is malformed, such as special characters or mixed invalid format    | HTTP 400, code=1001                                                                                                      |
| EP7 | id path parameter missing or empty path used instead of /users/{id}    | HTTP 400 or endpoint-level invalid request handling; per requirement classify as invalid parameter → HTTP 400, code=1001 |


**2) Boundary Value Analysis**


| Field | Boundary Values                                        |
| ----- | ------------------------------------------------------ |
| id    | Smallest valid positive integer, such as 1             |
| id    | Typical valid integer, such as 100                     |
| id    | Maximum supported integer value, if accepted by API    |
| id    | 0                                                      |
| id    | Negative integer, such as -1                           |
| id    | Just above valid lower boundary, such as 2             |
| id    | Non-integer numeric format, such as 1.5                |
| id    | Very large out-of-range integer, such as 999999999999  |
| id    | Empty / omitted value                                  |
| id    | Alphabetic or special-character input, such as abc, @# |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                     | Expected Result                                                                                                                       |
| ------------ | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| TC01         | Send GET /users/1 with a valid bearer token, and user 1 exists                               | HTTP 200, code=0; response data contains id, username, email, name, phone, website                                                    |
| TC02         | Send GET /users/99999 with a valid bearer token, where user does not exist                   | HTTP 404, code=2006                                                                                                                   |
| TC03         | Send GET /users/abc with a valid bearer token                                                | HTTP 400, code=1001                                                                                                                   |
| TC04         | Send GET /users/1.5 with a valid bearer token                                                | HTTP 400, code=1001                                                                                                                   |
| TC05         | Send GET /users/-1 with a valid bearer token                                                 | HTTP 400, code=1001                                                                                                                   |
| TC06         | Send GET /users/0 with a valid bearer token                                                  | HTTP 400, code=1001 or 404 if system accepts integer but user absent; based on invalid parameter expectation, use HTTP 400, code=1001 |
| TC07         | Send GET /users/ without providing the required id path parameter, with a valid bearer token | HTTP 400, code=1001                                                                                                                   |
| TC08         | Send GET /users/1 without bearer token                                                       | HTTP 401, code=401                                                                                                                    |
| TC09         | Send GET /users/1 with invalid or expired bearer token                                       | HTTP 401, code=401                                                                                                                    |
| TC10         | Send GET /users/2147483647 with a valid bearer token                                         | If user exists: HTTP 200, code=0; otherwise HTTP 404, code=2006                                                                       |


#### v2

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a structured testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome
Include both valid and invalid equivalence classes.
Boundary Value Analysis
Table columns: Field | Boundary Values
Include meaningful boundary values for the id parameter.
Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result
Requirements:

Cover valid case, invalid id, missing id, non-existing user,      missing token, and invalid token
Generate exactly 10 test cases
Keep      output concise and structured
```

**Output**

**1) Equivalence Partitioning**


| ID  | Description                                               | Outcome                                                                   |
| --- | --------------------------------------------------------- | ------------------------------------------------------------------------- |
| EP1 | Valid integer id, user exists, valid bearer token         | HTTP 200, code=0, data includes id, username, email, name, phone, website |
| EP2 | Valid integer id, user does not exist, valid bearer token | HTTP 404, code=2006                                                       |
| EP3 | id is non-integer (string/alphabetic)                     | HTTP 400, code=1001                                                       |
| EP4 | id is malformed numeric input (decimal/special chars)     | HTTP 400, code=1001                                                       |
| EP5 | id missing from path                                      | HTTP 400, code=1001                                                       |
| EP6 | Valid request but bearer token missing                    | HTTP 401, code=401                                                        |
| EP7 | Valid request but bearer token invalid/expired            | HTTP 401, code=401                                                        |


**2) Boundary Value Analysis**


| Field | Boundary Values                            |
| ----- | ------------------------------------------ |
| id    | Missing / empty                            |
| id    | -1                                         |
| id    | 0                                          |
| id    | 1                                          |
| id    | 2                                          |
| id    | Maximum typical 32-bit integer: 2147483647 |
| id    | Above typical integer range: 2147483648    |
| id    | Decimal value: 1.5                         |
| id    | Non-numeric value: abc                     |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                          | Expected Result                                                           |
| ------------ | ----------------------------------------------------------------- | ------------------------------------------------------------------------- |
| TC01         | GET /users/1 with valid bearer token, and user 1 exists           | HTTP 200, code=0; data contains id, username, email, name, phone, website |
| TC02         | GET /users/99999 with valid bearer token, but user does not exist | HTTP 404, code=2006                                                       |
| TC03         | GET /users/abc with valid bearer token                            | HTTP 400, code=1001                                                       |
| TC04         | GET /users/1.5 with valid bearer token                            | HTTP 400, code=1001                                                       |
| TC05         | GET /users/-1 with valid bearer token                             | HTTP 400, code=1001                                                       |
| TC06         | GET /users/0 with valid bearer token                              | HTTP 400, code=1001                                                       |
| TC07         | GET /users/ with valid bearer token but missing id path parameter | HTTP 400, code=1001                                                       |
| TC08         | GET /users/1 without bearer token                                 | HTTP 401, code=401                                                        |
| TC09         | GET /users/1 with invalid or expired bearer token                 | HTTP 401, code=401                                                        |
| TC10         | GET /users/2147483647 with valid bearer token                     | If user exists: HTTP 200, code=0; otherwise HTTP 404, code=2006           |


#### v3

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a formal software testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome
Clearly distinguish valid and invalid classes
Ensure coverage of parameter type, existence, and      authentication
Boundary Value Analysis
Table columns: Field | Boundary Values
Include meaningful boundary values for integer id (e.g.,      negative, zero, minimum positive, large value)
Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result
Requirements:

Use ONLY the provided requirements
Cover: normal case, invalid type, boundary values, non-existing      resource, missing token, invalid token
Generate exactly 10 high-quality test cases (no duplicates)
Each test case must correspond to a rule or boundary condition
Keep output concise, table-based, and suitable for a report
```

**Output**

**1) Equivalence Partitioning**


| ID  | Description                                                         | Outcome                                                                   |
| --- | ------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| EP1 | Valid:`id` is an integer, user exists, valid bearer token provided  | HTTP 200, code=0, data contains id, username, email, name, phone, website |
| EP2 | Invalid:`id` is an integer, token is valid, but user does not exist | HTTP 404, code=2006                                                       |
| EP3 | Invalid:`id` is not an integer (for example, alphabetic string)     | HTTP 400, code=1001                                                       |
| EP4 | Invalid:`id` uses invalid numeric format (for example, decimal)     | HTTP 400, code=1001                                                       |
| EP5 | Invalid: required path parameter`id` is missing                     | HTTP 400, code=1001                                                       |
| EP6 | Invalid: bearer token is missing                                    | HTTP 401, code=401                                                        |
| EP7 | Invalid: bearer token is invalid or expired                         | HTTP 401, code=401                                                        |


**2) Boundary Value Analysis**


| Field | Boundary Values                              |
| ----- | -------------------------------------------- |
| id    | -1                                           |
| id    | 0                                            |
| id    | 1 (minimum positive integer)                 |
| id    | 2                                            |
| id    | Large integer value, e.g. 2147483647         |
| id    | Above typical integer range, e.g. 2147483648 |
| id    | Decimal value, e.g. 1.5                      |
| id    | Non-numeric value, e.g. abc                  |
| id    | Missing / empty                              |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                   | Expected Result                                                           |
| ------------ | -------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| TC01         | Send GET /users/1 with a valid bearer token, where user 1 exists           | HTTP 200, code=0; data contains id, username, email, name, phone, website |
| TC02         | Send GET /users/99999 with a valid bearer token, where user does not exist | HTTP 404, code=2006                                                       |
| TC03         | Send GET /users/abc with a valid bearer token                              | HTTP 400, code=1001                                                       |
| TC04         | Send GET /users/1.5 with a valid bearer token                              | HTTP 400, code=1001                                                       |
| TC05         | Send GET /users/-1 with a valid bearer token                               | HTTP 400, code=1001                                                       |
| TC06         | Send GET /users/0 with a valid bearer token                                | HTTP 400, code=1001                                                       |
| TC07         | Send GET /users/2147483647 with a valid bearer token                       | If the user exists: HTTP 200, code=0; otherwise: HTTP 404, code=2006      |
| TC08         | Send GET /users/2147483648 with a valid bearer token                       | HTTP 400, code=1001                                                       |
| TC09         | Send GET /users/1 without a bearer token                                   | HTTP 401, code=401                                                        |
| TC10         | Send GET /users/1 with an invalid or expired bearer token                  | HTTP 401, code=401                                                        |


### 3.PUT /users/password

**Input**

```text
System Overview:
The system under test is a RESTful user management API. The internal implementation is not visible. Testing is performed based on API specifications.

Functional Requirements:

R1. Reset Password:
The system provides an API endpoint PUT /users/password to reset a user's password.

R2. Input Constraints:

email: required, valid email format
verificationCode: required, exactly 6 digits
newPassword: required, length 6–50 characters
R3. Business Rules:

The email must be registered in the system
The verification code must be valid and not expired
R4. Error Handling:

Email not registered → HTTP 400, code 2007
Invalid verification code → HTTP 400, code 2003
Invalid parameters → HTTP 400, code 1001
R5. Authentication:

This endpoint does NOT require login authentication
R6. Success Response:

HTTP 200
code = 0
```

#### v1

**Prompt**

```text
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

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                            | Outcome                                               |
| ---- | ---------------------------------------------------------------------- | ----------------------------------------------------- |
| EP1  | email provided, valid format, registered                               | Valid                                                 |
| EP2  | email missing                                                          | Invalid → HTTP 400, code 1001                         |
| EP3  | email format invalid                                                   | Invalid → HTTP 400, code 1001                         |
| EP4  | email valid format but not registered                                  | Invalid → HTTP 400, code 2007                         |
| EP5  | verificationCode provided, exactly 6 digits, valid, not expired        | Valid                                                 |
| EP6  | verificationCode missing                                               | Invalid → HTTP 400, code 1001                         |
| EP7  | verificationCode not exactly 6 digits or contains non-digit characters | Invalid → HTTP 400, code 1001                         |
| EP8  | verificationCode exactly 6 digits but invalid or expired               | Invalid → HTTP 400, code 2003                         |
| EP9  | newPassword length 6–50 characters                                     | Valid                                                 |
| EP10 | newPassword missing                                                    | Invalid → HTTP 400, code 1001                         |
| EP11 | newPassword length < 6                                                 | Invalid → HTTP 400, code 1001                         |
| EP12 | newPassword length > 50                                                | Invalid → HTTP 400, code 1001                         |
| EP13 | All inputs valid, no login/auth token provided                         | Valid, since endpoint does not require authentication |
| EP14 | All inputs valid and business rules satisfied                          | Success → HTTP 200, code 0                            |


**2) Boundary Value Analysis**


| Field            | Boundary Values                                                                             |
| ---------------- | ------------------------------------------------------------------------------------------- |
| email            | Missing, invalid format, valid format registered, valid format unregistered                 |
| verificationCode | Missing, 5 digits, 6 digits, 7 digits, 6 non-digit characters, 6 digits but invalid/expired |
| newPassword      | Missing, length 5, length 6, length 50, length 51                                           |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                          | Expected Result       |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| TC01         | Send request with registered email, valid 6-digit verification code, valid new password of length 6, without login authentication | HTTP 200, code = 0    |
| TC02         | Send request with registered email, valid verification code, valid new password of length 50                                      | HTTP 200, code = 0    |
| TC03         | Omit email from request                                                                                                           | HTTP 400, code = 1001 |
| TC04         | Provide email in invalid format, with other fields valid                                                                          | HTTP 400, code = 1001 |
| TC05         | Provide valid-format but unregistered email, with other fields valid                                                              | HTTP 400, code = 2007 |
| TC06         | Omit verificationCode from request                                                                                                | HTTP 400, code = 1001 |
| TC07         | Provide verificationCode with 5 digits                                                                                            | HTTP 400, code = 1001 |
| TC08         | Provide verificationCode with exactly 6 digits, but code is invalid or expired                                                    | HTTP 400, code = 2003 |
| TC09         | Provide newPassword with length 5                                                                                                 | HTTP 400, code = 1001 |
| TC10         | Provide newPassword with length 51                                                                                                | HTTP 400, code = 1001 |


#### v2

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a structured testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome
Include both valid and invalid equivalence classes.
Boundary Value Analysis
Table columns: Field | Boundary Values
Include boundary values for all input fields (email, verificationCode, newPassword).
Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result
Requirements:

Cover valid inputs, missing fields, boundary values, invalid      formats, and business errors
Generate exactly 10 test cases
Keep      output concise and structured
```

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                           | Outcome                           |
| ---- | --------------------------------------------------------------------- | --------------------------------- |
| EP1  | email is present, valid format, and registered                        | Valid                             |
| EP2  | email is missing                                                      | Invalid → HTTP 400, code 1001     |
| EP3  | email format is invalid                                               | Invalid → HTTP 400, code 1001     |
| EP4  | email format is valid but not registered                              | Invalid → HTTP 400, code 2007     |
| EP5  | verificationCode is present, exactly 6 digits, valid, and not expired | Valid                             |
| EP6  | verificationCode is missing                                           | Invalid → HTTP 400, code 1001     |
| EP7  | verificationCode is not exactly 6 digits                              | Invalid → HTTP 400, code 1001     |
| EP8  | verificationCode contains non-digit characters                        | Invalid → HTTP 400, code 1001     |
| EP9  | verificationCode is 6 digits but invalid or expired                   | Invalid → HTTP 400, code 2003     |
| EP10 | newPassword is present and length is 6–50 characters                  | Valid                             |
| EP11 | newPassword is missing                                                | Invalid → HTTP 400, code 1001     |
| EP12 | newPassword length is less than 6                                     | Invalid → HTTP 400, code 1001     |
| EP13 | newPassword length is greater than 50                                 | Invalid → HTTP 400, code 1001     |
| EP14 | Request sent without login/authentication                             | Valid, endpoint should still work |


**2) Boundary Value Analysis**


| Field            | Boundary Values                                                                                                |
| ---------------- | -------------------------------------------------------------------------------------------------------------- |
| email            | Missing, empty string, invalid format (e.g. user@, user.com), valid registered email, valid unregistered email |
| verificationCode | Missing, 5 digits, 6 digits, 7 digits, 6 non-digit chars, mixed alphanumeric 6 chars                           |
| newPassword      | Missing, empty string, length 5, length 6, length 50, length 51                                                |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                            | Expected Result       |
| ------------ | --------------------------------------------------------------------------------------------------- | --------------------- |
| TC01         | Registered email, valid 6-digit verification code, new password length = 6, no login authentication | HTTP 200, code = 0    |
| TC02         | Registered email, valid 6-digit verification code, new password length = 50                         | HTTP 200, code = 0    |
| TC03         | Missing email, other fields valid                                                                   | HTTP 400, code = 1001 |
| TC04         | Invalid email format, other fields valid                                                            | HTTP 400, code = 1001 |
| TC05         | Valid-format but unregistered email, other fields valid                                             | HTTP 400, code = 2007 |
| TC06         | Missing verificationCode, other fields valid                                                        | HTTP 400, code = 1001 |
| TC07         | verificationCode has 5 digits, other fields valid                                                   | HTTP 400, code = 1001 |
| TC08         | verificationCode is 6 digits but invalid or expired, other fields valid                             | HTTP 400, code = 2003 |
| TC09         | newPassword length = 5, other fields valid                                                          | HTTP 400, code = 1001 |
| TC10         | newPassword length = 51, other fields valid                                                         | HTTP 400, code = 1001 |


#### v3

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a formal software testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome
Clearly distinguish valid and invalid classes
Cover constraints: required fields, format validation, length      limits, and business rules
Boundary Value Analysis
Table columns: Field | Boundary Values
Include meaningful boundary values for each field
email: format-related boundaries
verificationCode: digit length boundaries
newPassword: length boundaries
Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result
Requirements:

Use ONLY the provided requirements
Cover: normal case, missing fields, boundary values, invalid      formats, unregistered email, and invalid verification code
Generate exactly 10 high-quality test cases (no duplicates)
Each test case must correspond to a rule or boundary condition
Keep output concise, table-based, and suitable for a report
```

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                            | Outcome                                     |
| ---- | ---------------------------------------------------------------------- | ------------------------------------------- |
| EP1  | email is provided, valid email format, and registered                  | Valid                                       |
| EP2  | email is missing                                                       | Invalid → HTTP 400, code 1001               |
| EP3  | email is in invalid format                                             | Invalid → HTTP 400, code 1001               |
| EP4  | email is valid format but not registered                               | Invalid → HTTP 400, code 2007               |
| EP5  | verificationCode is provided, exactly 6 digits, valid, and not expired | Valid                                       |
| EP6  | verificationCode is missing                                            | Invalid → HTTP 400, code 1001               |
| EP7  | verificationCode is not exactly 6 digits                               | Invalid → HTTP 400, code 1001               |
| EP8  | verificationCode contains non-digit characters                         | Invalid → HTTP 400, code 1001               |
| EP9  | verificationCode is exactly 6 digits but invalid or expired            | Invalid → HTTP 400, code 2003               |
| EP10 | newPassword is provided and length is 6–50 characters                  | Valid                                       |
| EP11 | newPassword is missing                                                 | Invalid → HTTP 400, code 1001               |
| EP12 | newPassword length is less than 6                                      | Invalid → HTTP 400, code 1001               |
| EP13 | newPassword length is greater than 50                                  | Invalid → HTTP 400, code 1001               |
| EP14 | Request is sent without login authentication                           | Valid, since authentication is not required |


**2) Boundary Value Analysis**


| Field            | Boundary Values                                                                                                                                                                      |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| email            | Missing, empty string, malformed email (for example user@, user.com), minimally valid format (for example [a@b.co](mailto:a@b.co)), valid registered email, valid unregistered email |
| verificationCode | Missing, 5 digits, exactly 6 digits, 7 digits, 6 characters with letters                                                                                                             |
| newPassword      | Missing, empty string, length 5, length 6, length 50, length 51                                                                                                                      |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                                            | Expected Result                                            |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| TC01         | Registered email, valid 6-digit verification code, valid new password of length 6, no authentication provided                                       | HTTP 200, code = 0                                         |
| TC02         | Registered email, valid 6-digit verification code, valid new password of length 50                                                                  | HTTP 200, code = 0                                         |
| TC03         | email is missing; verificationCode and newPassword are valid                                                                                        | HTTP 400, code = 1001                                      |
| TC04         | email format is invalid; verificationCode and newPassword are valid                                                                                 | HTTP 400, code = 1001                                      |
| TC05         | email is valid format but not registered; other fields are valid                                                                                    | HTTP 400, code = 2007                                      |
| TC06         | verificationCode is missing; email and newPassword are valid                                                                                        | HTTP 400, code = 1001                                      |
| TC07         | verificationCode has 5 digits; email and newPassword are valid                                                                                      | HTTP 400, code = 1001                                      |
| TC08         | verificationCode is 6 digits but invalid or expired; email and newPassword are valid                                                                | HTTP 400, code = 2003                                      |
| TC09         | newPassword length is 5; email and verificationCode are valid                                                                                       | HTTP 400, code = 1001                                      |
| TC10         | newPassword length is 51; email and verificationCode are valid                                                                                      | HTTP 400, code = 1001                                      |
| TC01         | Logged-in user submits valid request with required fields only: school, major, degree=3, startDate=2020-09-01                                       | HTTP 200, code = 0, data contains created education record |
| TC02         | Logged-in user submits valid request with all fields: school, major, degree=6, startDate=2020-09-01, endDate=2024-06-30, visible=true, gpa provided | HTTP 200, code = 0, data contains created education record |
| TC03         | User is not logged in and submits an otherwise valid request                                                                                        | HTTP 401, code = 1002                                      |
| TC04         | Logged-in user omits required field school                                                                                                          | HTTP 400, code = 1001                                      |
| TC05         | Logged-in user omits required field startDate                                                                                                       | HTTP 400, code = 1001                                      |
| TC06         | Logged-in user submits boundary value degree=1 with other fields valid                                                                              | HTTP 200, code = 0, data contains created education record |
| TC07         | Logged-in user submits boundary value degree=0 with other fields valid                                                                              | HTTP 400, code = 1001                                      |
| TC08         | Logged-in user submits boundary value degree=7 with other fields valid                                                                              | HTTP 400, code = 1001                                      |
| TC09         | Logged-in user submits invalid startDate format: 2020/09/01                                                                                         | HTTP 400, code = 1001                                      |
| TC10         | Logged-in user submits startDate=2022-09-01 and endDate=2022-08-31                                                                                  | HTTP 400, code = 6003                                      |


### 4.POST /educations

**Input**

```text
System Overview:
The system under test is a RESTful API for managing user education experiences. The internal implementation is not visible. Testing is performed based on API specifications.

Functional Requirements:

R1. Create Education:
The system provides an API endpoint POST /educations to add a new education record for a user.

R2. Input Constraints:

school: required
major: required
degree: required, integer, range 1–6
startDate: required, format yyyy-MM-dd
endDate: optional, format yyyy-MM-dd
visible: optional, boolean
gpa: optional
R3. Business Rules:

The user must be logged in
startDate must be earlier than or equal to endDate (if endDate      is provided)
R4. Error Handling:

Not logged in → HTTP 401, code 1002
Invalid parameters → HTTP 400, code 1001
Invalid date logic (endDate earlier than startDate) → HTTP 400,      code 6003
R5. Success Response:

HTTP 200
code = 0
data      contains education record
```

#### v1

**Prompt**

```text
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

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                           | Outcome                       |
| ---- | --------------------------------------------------------------------- | ----------------------------- |
| EP1  | All required fields valid; optional fields omitted                    | Valid, should succeed         |
| EP2  | All required fields valid; optional fields provided with valid values | Valid, should succeed         |
| EP3  | school missing                                                        | Invalid, HTTP 400 / code 1001 |
| EP4  | major missing                                                         | Invalid, HTTP 400 / code 1001 |
| EP5  | degree missing                                                        | Invalid, HTTP 400 / code 1001 |
| EP6  | degree is not an integer                                              | Invalid, HTTP 400 / code 1001 |
| EP7  | degree integer within range 1–6                                       | Valid                         |
| EP8  | degree less than 1 or greater than 6                                  | Invalid, HTTP 400 / code 1001 |
| EP9  | startDate missing                                                     | Invalid, HTTP 400 / code 1001 |
| EP10 | startDate in invalid format                                           | Invalid, HTTP 400 / code 1001 |
| EP11 | endDate omitted                                                       | Valid                         |
| EP12 | endDate in valid format and startDate <= endDate                      | Valid                         |
| EP13 | endDate in invalid format                                             | Invalid, HTTP 400 / code 1001 |
| EP14 | endDate earlier than startDate                                        | Invalid, HTTP 400 / code 6003 |
| EP15 | visible omitted                                                       | Valid                         |
| EP16 | visible is boolean true/false                                         | Valid                         |
| EP17 | visible is non-boolean                                                | Invalid, HTTP 400 / code 1001 |
| EP18 | gpa omitted                                                           | Valid                         |
| EP19 | gpa provided with acceptable value/type                               | Valid                         |
| EP20 | User not logged in                                                    | Invalid, HTTP 401 / code 1002 |


**2) Boundary Value Analysis**


| Field     | Boundary Values                                                                                        |
| --------- | ------------------------------------------------------------------------------------------------------ |
| degree    | 0, 1, 2, 5, 6, 7                                                                                       |
| startDate | Valid date format minimum structure yyyy-MM-dd; invalid neighboring forms such as yyyy-M-d, yyyy/MM/dd |
| endDate   | Omitted, equal to startDate, one day before startDate, one day after startDate                         |
| visible   | true, false, invalid values such as "true" or 1                                                        |
| school    | Present non-empty value, missing                                                                       |
| major     | Present non-empty value, missing                                                                       |
| gpa       | Omitted, provided valid value/type                                                                     |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                                          | Expected Result                                          |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| TC01         | Logged-in user sends valid request with required fields only: school, major, degree=3, startDate=2020-09-01                                       | HTTP 200, code=0, data contains created education record |
| TC02         | Logged-in user sends valid request with all fields: school, major, degree=6, startDate=2020-09-01, endDate=2024-06-30, visible=true, gpa provided | HTTP 200, code=0, data contains created education record |
| TC03         | User is not logged in and sends otherwise valid request                                                                                           | HTTP 401, code=1002                                      |
| TC04         | Logged-in user omits required field school                                                                                                        | HTTP 400, code=1001                                      |
| TC05         | Logged-in user sets degree=0                                                                                                                      | HTTP 400, code=1001                                      |
| TC06         | Logged-in user sets degree=1                                                                                                                      | HTTP 200, code=0, data contains created education record |
| TC07         | Logged-in user sets degree=6                                                                                                                      | HTTP 200, code=0, data contains created education record |
| TC08         | Logged-in user sets degree=7                                                                                                                      | HTTP 400, code=1001                                      |
| TC09         | Logged-in user provides startDate in invalid format, e.g. 2020/09/01                                                                              | HTTP 400, code=1001                                      |
| TC10         | Logged-in user provides endDate earlier than startDate, e.g. startDate=2022-09-01, endDate=2022-08-31                                             | HTTP 400, code=6003                                      |


#### v2

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a structured testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome
Include both valid and invalid equivalence classes.
Boundary Value Analysis
Table columns: Field | Boundary Values
Include boundary values for all input fields (degree, startDate, endDate).
Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result
Requirements:

Cover valid inputs, missing fields, boundary values, invalid      formats, and business errors
Include authentication scenario (logged in vs not logged in)
Generate exactly 10 test cases
Keep      output concise and structured
```

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                        | Outcome                       |
| ---- | ------------------------------------------------------------------ | ----------------------------- |
| EP1  | Logged-in user, all required fields valid, optional fields omitted | Valid                         |
| EP2  | Logged-in user, all fields valid including optional fields         | Valid                         |
| EP3  | Not logged in, request otherwise valid                             | Invalid → HTTP 401, code 1002 |
| EP4  | school missing                                                     | Invalid → HTTP 400, code 1001 |
| EP5  | major missing                                                      | Invalid → HTTP 400, code 1001 |
| EP6  | degree missing                                                     | Invalid → HTTP 400, code 1001 |
| EP7  | degree is integer within 1–6                                       | Valid                         |
| EP8  | degree less than 1                                                 | Invalid → HTTP 400, code 1001 |
| EP9  | degree greater than 6                                              | Invalid → HTTP 400, code 1001 |
| EP10 | degree not an integer                                              | Invalid → HTTP 400, code 1001 |
| EP11 | startDate missing                                                  | Invalid → HTTP 400, code 1001 |
| EP12 | startDate valid format yyyy-MM-dd                                  | Valid                         |
| EP13 | startDate invalid format                                           | Invalid → HTTP 400, code 1001 |
| EP14 | endDate omitted                                                    | Valid                         |
| EP15 | endDate valid format and startDate <= endDate                      | Valid                         |
| EP16 | endDate invalid format                                             | Invalid → HTTP 400, code 1001 |
| EP17 | endDate earlier than startDate                                     | Invalid → HTTP 400, code 6003 |
| EP18 | endDate equal to startDate                                         | Valid                         |
| EP19 | visible omitted or boolean value provided                          | Valid                         |
| EP20 | visible provided as non-boolean                                    | Invalid → HTTP 400, code 1001 |
| EP21 | gpa omitted or provided                                            | Valid                         |


**2) Boundary Value Analysis**


| Field     | Boundary Values                                                                                                   |
| --------- | ----------------------------------------------------------------------------------------------------------------- |
| degree    | 0, 1, 2, 5, 6, 7                                                                                                  |
| startDate | Valid format boundary examples: 2020-01-01; invalid format examples: 2020/01/01, 01-01-2020                       |
| endDate   | Omitted; equal to startDate; one day after startDate; one day before startDate; invalid format such as 2020/12/31 |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                                            | Expected Result                                          |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| TC01         | Logged-in user submits valid request with required fields only: school, major, degree=3, startDate=2020-09-01                                       | HTTP 200, code=0, data contains created education record |
| TC02         | Logged-in user submits valid request with all fields: school, major, degree=6, startDate=2020-09-01, endDate=2024-06-30, visible=true, gpa provided | HTTP 200, code=0, data contains created education record |
| TC03         | Not logged in, submits otherwise valid request                                                                                                      | HTTP 401, code=1002                                      |
| TC04         | Logged-in user omits school                                                                                                                         | HTTP 400, code=1001                                      |
| TC05         | Logged-in user omits major                                                                                                                          | HTTP 400, code=1001                                      |
| TC06         | Logged-in user sets degree=0                                                                                                                        | HTTP 400, code=1001                                      |
| TC07         | Logged-in user sets degree=1                                                                                                                        | HTTP 200, code=0, data contains created education record |
| TC08         | Logged-in user sets degree=7                                                                                                                        | HTTP 400, code=1001                                      |
| TC09         | Logged-in user provides invalid startDate format: 2020/09/01                                                                                        | HTTP 400, code=1001                                      |
| TC10         | Logged-in user provides startDate=2022-09-01, endDate=2022-08-31                                                                                    | HTTP 400, code=6003                                      |


#### v3

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a formal software testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome
Clearly distinguish valid and invalid classes
Cover constraints: required fields, value range, date format,      and business rules
Boundary Value Analysis
Table columns: Field | Boundary Values
Include meaningful boundary values
degree: range boundaries (1–6)
startDate and endDate: logical boundaries and format
Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result
Requirements:

Use ONLY the provided requirements
Cover: normal case, missing fields, boundary values, invalid      formats, invalid date logic, and unauthorized access
Generate exactly 10 high-quality test cases (no duplicates)
Each test case must correspond to a rule or boundary condition
Keep output concise, table-based, and suitable for a report
```

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                           | Outcome                         |
| ---- | ------------------------------------------------------------------------------------- | ------------------------------- |
| EP01 | Logged-in user submits all required fields with valid values; optional fields omitted | Valid → HTTP 200, code = 0      |
| EP02 | Logged-in user submits all required fields plus optional fields with valid values     | Valid → HTTP 200, code = 0      |
| EP03 | User is not logged in                                                                 | Invalid → HTTP 401, code = 1002 |
| EP04 | school missing                                                                        | Invalid → HTTP 400, code = 1001 |
| EP05 | major missing                                                                         | Invalid → HTTP 400, code = 1001 |
| EP06 | degree missing                                                                        | Invalid → HTTP 400, code = 1001 |
| EP07 | degree is an integer within range 1–6                                                 | Valid                           |
| EP08 | degree is less than 1                                                                 | Invalid → HTTP 400, code = 1001 |
| EP09 | degree is greater than 6                                                              | Invalid → HTTP 400, code = 1001 |
| EP10 | degree is not an integer                                                              | Invalid → HTTP 400, code = 1001 |
| EP11 | startDate missing                                                                     | Invalid → HTTP 400, code = 1001 |
| EP12 | startDate is in valid yyyy-MM-dd format                                               | Valid                           |
| EP13 | startDate is in invalid format                                                        | Invalid → HTTP 400, code = 1001 |
| EP14 | endDate omitted                                                                       | Valid                           |
| EP15 | endDate provided in valid yyyy-MM-dd format and startDate <= endDate                  | Valid                           |
| EP16 | endDate provided in invalid format                                                    | Invalid → HTTP 400, code = 1001 |
| EP17 | endDate < startDate                                                                   | Invalid → HTTP 400, code = 6003 |
| EP18 | endDate = startDate                                                                   | Valid                           |
| EP19 | visible omitted or boolean (true/false)                                               | Valid                           |
| EP20 | visible is non-boolean                                                                | Invalid → HTTP 400, code = 1001 |
| EP21 | gpa omitted or provided                                                               | Valid                           |


**2) Boundary Value Analysis**


| Field     | Boundary Values                                                                                                                                           |
| --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| degree    | 0, 1, 2, 5, 6, 7                                                                                                                                          |
| startDate | Valid format: 2024-01-01; invalid format examples: 2024/01/01, 01-01-2024; logical comparison points when endDate exists: < endDate, = endDate, > endDate |
| endDate   | Omitted; equal to startDate; one day after startDate; one day before startDate; invalid format examples: 2024/12/31, 31-12-2024                           |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                                            | Expected Result                                            |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| TC01         | Logged-in user submits valid request with required fields only: school, major, degree=3, startDate=2020-09-01                                       | HTTP 200, code = 0, data contains created education record |
| TC02         | Logged-in user submits valid request with all fields: school, major, degree=6, startDate=2020-09-01, endDate=2024-06-30, visible=true, gpa provided | HTTP 200, code = 0, data contains created education record |
| TC03         | User is not logged in and submits an otherwise valid request                                                                                        | HTTP 401, code = 1002                                      |
| TC04         | Logged-in user omits required field school                                                                                                          | HTTP 400, code = 1001                                      |
| TC05         | Logged-in user omits required field startDate                                                                                                       | HTTP 400, code = 1001                                      |
| TC06         | Logged-in user submits boundary value degree=1 with other fields valid                                                                              | HTTP 200, code = 0, data contains created education record |
| TC07         | Logged-in user submits boundary value degree=0 with other fields valid                                                                              | HTTP 400, code = 1001                                      |
| TC08         | Logged-in user submits boundary value degree=7 with other fields valid                                                                              | HTTP 400, code = 1001                                      |
| TC09         | Logged-in user submits invalid startDate format: 2020/09/01                                                                                         | HTTP 400, code = 1001                                      |
| TC10         | Logged-in user submits startDate=2022-09-01 and endDate=2022-08-31                                                                                  | HTTP 400, code = 6003                                      |


### 5.PUT /educations

**Input**

```text
System Overview:
The system under test is a RESTful API for managing user education experiences. The internal implementation is not visible. Testing is performed based on API specifications.
Functional Requirements:
R1. Update Education:
The system provides an API endpoint PUT /educations to update an existing education record.
R2. Input Constraints:
* id: required, integer
* school: optional
* major: optional
* degree: optional, integer, range 1–6 if provided
* startDate: optional, format yyyy-MM-dd if provided
* endDate: optional, format yyyy-MM-dd if provided
* visible: optional, boolean
* gpa: optional
R3. Business Rules:
* The user must be logged in
* The education record must exist
* The user can only update their own education record
* If both startDate and endDate are provided, startDate must be earlier than or equal to endDate
R4. Error Handling:
* Education id is missing → HTTP 400, code 6002
* Invalid date logic (endDate earlier than startDate) → HTTP 400, code 6003
* Education record does not exist → HTTP 404, code 6006
* Not logged in → HTTP 401, code 1002
* No permission to update another user's education → HTTP 403, code 1003
R5. Success Response:
* HTTP 200
* code = 0
* data contains updated education record
```

#### v1

**Prompt**

```text
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

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                                                                                                                                   | Outcome                                  |
| ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| EP1  | Valid update request: user is logged in, education record exists, belongs to user, id is a valid integer, all provided fields are valid, and startDate <= endDate when both dates are present | Valid                                    |
| EP2  | id is missing from request                                                                                                                                                                    | Invalid, HTTP 400, code 6002             |
| EP3  | id is provided as a non-integer value                                                                                                                                                         | Invalid                                  |
| EP4  | degree is omitted                                                                                                                                                                             | Valid                                    |
| EP5  | degree is an integer within 1–6                                                                                                                                                               | Valid                                    |
| EP6  | degree is less than 1 or greater than 6                                                                                                                                                       | Invalid                                  |
| EP7  | startDate or endDate is omitted                                                                                                                                                               | Valid, as long as other fields are valid |
| EP8  | startDate and endDate are both valid dates and startDate <= endDate                                                                                                                           | Valid                                    |
| EP9  | startDate and/or endDate has invalid format (not yyyy-MM-dd)                                                                                                                                  | Invalid                                  |
| EP10 | startDate > endDate                                                                                                                                                                           | Invalid, HTTP 400, code 6003             |
| EP11 | visible is a boolean (true / false)                                                                                                                                                           | Valid                                    |
| EP12 | visible is not a boolean                                                                                                                                                                      | Invalid                                  |
| EP13 | User is not logged in                                                                                                                                                                         | Invalid, HTTP 401, code 1002             |
| EP14 | Education record does not exist for given id                                                                                                                                                  | Invalid, HTTP 404, code 6006             |
| EP15 | Logged-in user tries to update another user's education record                                                                                                                                | Invalid, HTTP 403, code 1003             |


**2) Boundary Value Analysis**


| Field                | Boundary Values                                                                                                                |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| id                   | Missing, 0, 1, maximum valid integer, non-integer                                                                              |
| degree               | Omitted, 0, 1, 2, 5, 6, 7                                                                                                      |
| startDate / endDate  | Valid formatted date, invalid formatted date, startDate = endDate, startDate just before endDate, startDate just after endDate |
| visible              | true, false, invalid non-boolean value                                                                                         |
| school / major / gpa | Omitted, present with valid value                                                                                              |
| Authorization        | Not logged in, logged in as owner, logged in as non-owner                                                                      |
| Record existence     | Existing id, non-existing id                                                                                                   |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                                        | Expected Result                                                                  |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| TC01         | Update own existing education with valid id, school, major, degree=3, startDate=2020-09-01, endDate=2024-06-30, visible=true, user is logged in | HTTP 200, code = 0, response data contains updated education record              |
| TC02         | Update own existing education with only required field id, user is logged in                                                                    | HTTP 200, code = 0, response data contains education record updated successfully |
| TC03         | Send update request without id, user is logged in                                                                                               | HTTP 400, code = 6002                                                            |
| TC04         | Send update request with degree=1 (lower valid boundary), user is logged in and owns record                                                     | HTTP 200, code = 0                                                               |
| TC05         | Send update request with degree=6 (upper valid boundary), user is logged in and owns record                                                     | HTTP 200, code = 0                                                               |
| TC06         | Send update request with degree=0                                                                                                               | Request rejected as invalid input                                                |
| TC07         | Send update request with startDate=2024-06-01 and endDate=2024-06-01                                                                            | HTTP 200, code = 0, because startDate = endDate is allowed                       |
| TC08         | Send update request with startDate=2024-06-02 and endDate=2024-06-01                                                                            | HTTP 400, code = 6003                                                            |
| TC09         | Send update request for a non-existing education id, user is logged in                                                                          | HTTP 404, code = 6006                                                            |
| TC10         | Send update request for another user's education record while logged in                                                                         | HTTP 403, code = 1003                                                            |


#### v2

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a structured testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome
Include both valid and invalid equivalence classes.
Boundary Value Analysis
Table columns: Field | Boundary Values
Include boundary values for important fields such as id and degree, and logical boundaries for dates.
Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result
Requirements:

Cover valid inputs, missing required fields, boundary values,      invalid formats, authentication errors, and authorization errors
Generate exactly 10 test cases
Keep      output concise and structured
```

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                                     | Outcome                      |
| ---- | ----------------------------------------------------------------------------------------------- | ---------------------------- |
| EP01 | Logged-in user updates their own existing education with valid id and all provided fields valid | Valid                        |
| EP02 | Logged-in user updates their own existing education with only required field id                 | Valid                        |
| EP03 | id is missing                                                                                   | Invalid, HTTP 400, code 6002 |
| EP04 | id is non-integer                                                                               | Invalid                      |
| EP05 | degree omitted                                                                                  | Valid                        |
| EP06 | degree is integer within 1–6                                                                    | Valid                        |
| EP07 | degree is less than 1                                                                           | Invalid                      |
| EP08 | degree is greater than 6                                                                        | Invalid                      |
| EP09 | startDate and endDate both valid and startDate <= endDate                                       | Valid                        |
| EP10 | startDate or endDate has invalid format                                                         | Invalid                      |
| EP11 | startDate > endDate                                                                             | Invalid, HTTP 400, code 6003 |
| EP12 | visible is boolean (true/false)                                                                 | Valid                        |
| EP13 | visible is non-boolean                                                                          | Invalid                      |
| EP14 | User not logged in                                                                              | Invalid, HTTP 401, code 1002 |
| EP15 | Education record does not exist                                                                 | Invalid, HTTP 404, code 6006 |
| EP16 | Logged-in user updates another user's education                                                 | Invalid, HTTP 403, code 1003 |


**2) Boundary Value Analysis**


| Field               | Boundary Values                                                                                        |
| ------------------- | ------------------------------------------------------------------------------------------------------ |
| id                  | Missing, non-integer, 0, 1, large valid integer, non-existing integer                                  |
| degree              | Omitted, 0, 1, 2, 5, 6, 7                                                                              |
| startDate / endDate | Valid format yyyy-MM-dd, invalid format, startDate = endDate, startDate < endDate, startDate > endDate |
| visible             | true, false, invalid value such as "yes" or 1                                                          |
| Authentication      | Logged in, not logged in                                                                               |
| Authorization       | Owner of record, non-owner of record                                                                   |
| Record existence    | Existing record, non-existing record                                                                   |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                                        | Expected Result                                                          |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| TC01         | Logged-in user updates own existing education with valid id, school, major, degree=3, startDate=2020-09-01, endDate=2024-06-30, visible=true    | HTTP 200, code=0, data contains updated education record                 |
| TC02         | Logged-in user updates own existing education with only id provided                                                                             | HTTP 200, code=0, data contains updated education record                 |
| TC03         | Logged-in user sends request without id                                                                                                         | HTTP 400, code=6002                                                      |
| TC04         | Logged-in user sends request with non-integer id                                                                                                | Request rejected as invalid input                                        |
| TC05         | Logged-in user updates own existing education with degree=1                                                                                     | HTTP 200, code=0                                                         |
| TC06         | Logged-in user updates own existing education with degree=6                                                                                     | HTTP 200, code=0                                                         |
| TC07         | Logged-in user updates own existing education with degree=0                                                                                     | Request rejected as invalid input                                        |
| TC08         | Logged-in user updates own existing education with invalid date format, e.g. startDate=2024/01/01                                               | Request rejected as invalid input                                        |
| TC09         | Logged-in user updates own existing education with startDate=2024-06-02, endDate=2024-06-01                                                     | HTTP 400, code=6003                                                      |
| TC10         | Access control checks: not logged in → update request; logged-in non-owner → update another user's record; logged-in owner with non-existing id | Respectively: HTTP 401 code=1002; HTTP 403 code=1003; HTTP 404 code=6006 |


#### v3

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a formal software testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome
Clearly distinguish valid and invalid classes
Cover constraints: required id, optional fields, value range,      date format, authentication, authorization, and resource existence
Boundary Value Analysis
Table columns: Field | Boundary Values
Include meaningful boundary values
id: presence and valid integer considerations
degree: range boundaries (1–6)
startDate and endDate: logical and format boundaries
Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result
Requirements:

Use ONLY the provided requirements
Cover: normal update, missing id, invalid degree, invalid date      logic, non-existing education, unauthorized access, forbidden update of      another user's record, and partial update cases
Generate exactly 10 high-quality test cases (no duplicates)
Each test case must correspond to a rule or boundary condition
Keep output concise, table-based, and suitable for a report
```

**Output**

**1) Equivalence Partitioning**


| ID    | Description                                                                                                | Outcome                      |
| ----- | ---------------------------------------------------------------------------------------------------------- | ---------------------------- |
| EP01  | Valid: Logged-in user updates their own existing education record with valid`id` and valid optional fields | Valid                        |
| EP025 | Valid: Logged-in user performs partial update with only`id` and one or more optional fields omitted        | Valid                        |
| EP03  | Invalid:`id` is missing                                                                                    | Invalid, HTTP 400, code 6002 |
| EP04  | Invalid:`id` is present but not an integer                                                                 | Invalid                      |
| EP05  | Valid:`degree` omitted                                                                                     | Valid                        |
| EP06  | Valid:`degree` provided as integer within range `1–6`                                                      | Valid                        |
| EP07  | Invalid:`degree` provided as integer less than `1` or greater than `6`                                     | Invalid                      |
| EP08  | Valid:`startDate` and/or `endDate` omitted                                                                 | Valid                        |
| EP09  | Valid:`startDate` and `endDate` both provided in `yyyy-MM-dd` format and `startDate <= endDate`            | Valid                        |
| EP10  | Invalid:`startDate` and/or `endDate` provided in invalid format                                            | Invalid                      |
| EP11  | Invalid:`startDate > endDate`                                                                              | Invalid, HTTP 400, code 6003 |
| EP12  | Valid:`visible` provided as boolean                                                                        | Valid                        |
| EP13  | Invalid:`visible` provided as non-boolean                                                                  | Invalid                      |
| EP14  | Invalid: User is not logged in                                                                             | Invalid, HTTP 401, code 1002 |
| EP15  | Invalid: Education record does not exist                                                                   | Invalid, HTTP 404, code 6006 |
| EP16  | Invalid: Logged-in user attempts to update another user’s education record                                 | Invalid, HTTP 403, code 1003 |


**2) Boundary Value Analysis**


| Field                      | Boundary Values                                               |
| -------------------------- | ------------------------------------------------------------- |
| id                         | Missing, valid integer present, non-integer                   |
| degree                     | Omitted, 0, 1, 6, 7                                           |
| startDate / endDate format | Valid yyyy-MM-dd, invalid format such as 2024/01/01           |
| startDate / endDate logic  | startDate < endDate, startDate = endDate, startDate > endDate |
| visible                    | true, false, invalid non-boolean                              |
| Record existence           | Existing id, non-existing id                                  |
| Access state               | Logged in as owner, not logged in, logged in as non-owner     |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                                           | Expected Result                                          |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| TC01         | Logged-in user updates their own existing education with valid id, school, major, degree=3, startDate=2020-09-01, endDate=2024-06-30, visible=true | HTTP 200, code 0, data contains updated education record |
| TC02         | Logged-in user performs partial update on own existing education with valid id and only school updated                                             | HTTP 200, code 0, data contains updated education record |
| TC03         | Logged-in user sends update request without id                                                                                                     | HTTP 400, code 6002                                      |
| TC04         | Logged-in user updates own existing education with degree=1                                                                                        | HTTP 200, code 0                                         |
| TC05         | Logged-in user updates own existing education with degree=6                                                                                        | HTTP 200, code 0                                         |
| TC06         | Logged-in user updates own existing education with invalid degree=0                                                                                | Request rejected as invalid input                        |
| TC07         | Logged-in user updates own existing education with invalid degree=7                                                                                | Request rejected as invalid input                        |
| TC08         | Logged-in user updates own existing education with valid date format but invalid logic: startDate=2024-06-02, endDate=2024-06-01                   | HTTP 400, code 6003                                      |
| TC09         | Logged-in user updates education using a non-existing id                                                                                           | HTTP 404, code 6006                                      |
| TC10         | Access control checks: not logged in when updating any education; logged-in user updates another user’s education                                  | Respectively: HTTP 401, code 1002; HTTP 403, code 1003   |


### 6.GET /jobs

**Input**

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

#### v1

**Prompt**

```text
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

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                 | Outcome                                                                 |
| ---- | --------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| EP1  | No query parameters provided                                                | Valid, HTTP 200, defaults applied: page=1, limit=20                     |
| EP2  | page is a positive integer                                                  | Valid, HTTP 200                                                         |
| EP3  | page is 0 or negative integer                                               | Valid, auto-corrected to 1, HTTP 200                                    |
| EP4  | page is non-integer or wrong type                                           | Invalid, HTTP 400, code=1001                                            |
| EP5  | limit is an integer between 1 and 100                                       | Valid, HTTP 200                                                         |
| EP6  | limit is 0 or negative integer                                              | Valid, auto-corrected to 20, HTTP 200                                   |
| EP7  | limit is greater than 100                                                   | Valid, truncated to 100, HTTP 200                                       |
| EP8  | limit is non-integer or wrong type                                          | Invalid, HTTP 400, code=1001                                            |
| EP9  | companySizes is an array of integers                                        | Valid, HTTP 200                                                         |
| EP10 | companySizes is not an array of integers                                    | Invalid, HTTP 400, code=1001                                            |
| EP11 | fundingTypes is an array of integers                                        | Valid, HTTP 200                                                         |
| EP12 | fundingTypes is not an array of integers                                    | Invalid, HTTP 400, code=1001                                            |
| EP13 | industries is an array of strings                                           | Valid, HTTP 200                                                         |
| EP14 | industries is not an array of strings                                       | Invalid, HTTP 400, code=1001                                            |
| EP15 | companyName is a string                                                     | Valid, HTTP 200                                                         |
| EP16 | companyName is not a string                                                 | Invalid, HTTP 400, code=1001                                            |
| EP17 | employment is INTERN or FULL_TIME                                           | Valid, HTTP 200                                                         |
| EP18 | employment is omitted                                                       | Valid, no employment restriction, HTTP 200                            |
| EP19 | employment is outside enum or wrong type                                    | Invalid, HTTP 400, code=1001                                            |
| EP20 | filterSalaryMin / filterSalaryMax are integers                              | Valid, HTTP 200                                                         |
| EP21 | filterSalaryMin / filterSalaryMax are non-integer or wrong type             | Invalid, HTTP 400, code=1001                                            |
| EP22 | jobName is a string                                                         | Valid, inclusion match applied, HTTP 200                                |
| EP23 | jobName is not a string                                                     | Invalid, HTTP 400, code=1001                                            |
| EP24 | Valid combination of pagination and filters                                 | Valid, HTTP 200, filtered paginated result returned                     |


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


| Test Case ID | Scenario                                                                                                                                      | Expected Result                                                                                         |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| TC01         | Call GET /jobs with no parameters                                                                                                             | HTTP 200; code=0; data contains list, total, page=1, limit=20, totalPages                                 |
| TC02         | Call GET /jobs?page=2&limit=50                                                                                                                | HTTP 200; code=0; response shows page=2, limit=50; data structure is correct                          |
| TC03         | Call GET /jobs?page=0&limit=0                                                                                                                 | HTTP 200; code=0; system auto-corrects to page=1, limit=20                                              |
| TC04         | Call GET /jobs?page=-5&limit=-10                                                                                                              | HTTP 200; code=0; system auto-corrects to page=1, limit=20                                              |
| TC05         | Call GET /jobs?limit=101                                                                                                                      | HTTP 200; code=0; system truncates limit to 100                                                         |
| TC06         | Call GET /jobs?page=abc                                                                                                                       | HTTP 400; code=1001                                                                                     |
| TC07         | Call GET /jobs?employment=PART_TIME                                                                                                           | HTTP 400; code=1001                                                                                     |
| TC08         | Call GET /jobs?companySizes=small or companySizes=[1,"a"]                                                                                     | HTTP 400; code=1001                                                                                     |
| TC09         | Call GET /jobs?employment=FULL_TIME&jobName=engineer&companyName=Tech&industries=["AI"]                                                       | HTTP 200; code=0; returned jobs satisfy filters; jobName uses inclusion match                          |
| TC10         | Call GET /jobs?filterSalaryMin=5000&filterSalaryMax=10000&fundingTypes=[1,2]&companySizes=[50,100]                                              | HTTP 200; code=0; filtered paginated result returned with correct response structure                    |


#### v2

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a structured testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome
Include both valid and invalid equivalence classes.

Boundary Value Analysis
Table columns: Field | Boundary Values
Include boundary values for pagination and filter-related      parameters, especially page, limit, and employment.

Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result

Requirements:

Cover valid inputs, corrected boundary cases, invalid formats,      invalid enum values, and filter scenarios
Generate exactly 10 test cases
Keep      output concise and structured
```

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                 | Outcome                                                                 |
| ---- | --------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| EP1  | No parameters provided                                                      | Valid, HTTP 200, defaults applied: page=1, limit=20                     |
| EP2  | page is a positive integer                                                  | Valid, HTTP 200                                                         |
| EP3  | page is 0 or negative integer                                               | Valid, corrected to 1, HTTP 200                                       |
| EP4  | page is non-integer or wrong type                                           | Invalid, HTTP 400, code=1001                                            |
| EP5  | limit is integer in range 1..100                                            | Valid, HTTP 200                                                         |
| EP6  | limit is 0 or negative integer                                              | Valid, corrected to 20, HTTP 200                                        |
| EP7  | limit is greater than 100                                                     | Valid, truncated to 100, HTTP 200                                       |
| EP8  | limit is non-integer or wrong type                                          | Invalid, HTTP 400, code=1001                                            |
| EP9  | companySizes is an array of integers                                        | Valid, HTTP 200                                                         |
| EP10 | companySizes contains non-integer or is wrong type                          | Invalid, HTTP 400, code=1001                                            |
| EP11 | fundingTypes is an array of integers                                        | Valid, HTTP 200                                                         |
| EP12 | fundingTypes contains non-integer or is wrong type                          | Invalid, HTTP 400, code=1001                                            |
| EP13 | industries is an array of strings                                           | Valid, HTTP 200                                                         |
| EP14 | industries contains non-string or is wrong type                             | Invalid, HTTP 400, code=1001                                            |
| EP15 | companyName is a string                                                       | Valid, HTTP 200                                                         |
| EP16 | companyName is wrong type                                                   | Invalid, HTTP 400, code=1001                                            |
| EP17 | employment is INTERN or FULL_TIME                                           | Valid, HTTP 200                                                         |
| EP18 | employment is omitted                                                       | Valid, no employment restriction, HTTP 200                            |
| EP19 | employment has invalid enum value or wrong type                             | Invalid, HTTP 400, code=1001                                            |
| EP20 | filterSalaryMin / filterSalaryMax are integers                              | Valid, HTTP 200                                                         |
| EP21 | filterSalaryMin / filterSalaryMax are wrong type                            | Invalid, HTTP 400, code=1001                                            |
| EP22 | jobName is a string                                                         | Valid, inclusion match, HTTP 200                                      |
| EP23 | jobName is wrong type                                                       | Invalid, HTTP 400, code=1001                                            |
| EP24 | Multiple valid filters used together                                        | Valid, HTTP 200, filtered paginated result                              |


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


| Test Case ID | Scenario                                                                                                                                      | Expected Result                                                                                         |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| TC01         | GET /jobs with no parameters                                                                                                                  | HTTP 200, code=0, data contains list, total, page=1, limit=20, totalPages                                 |
| TC02         | GET /jobs?page=2&limit=50                                                                                                                     | HTTP 200, code=0, response shows page=2, limit=50                                                       |
| TC03         | GET /jobs?page=0&limit=0                                                                                                                      | HTTP 200, code=0, corrected to page=1, limit=20                                                         |
| TC04         | GET /jobs?page=-3&limit=101                                                                                                                   | HTTP 200, code=0, corrected to page=1, truncated to limit=100                                           |
| TC05         | GET /jobs?page=abc                                                                                                                            | HTTP 400, code=1001                                                                                     |
| TC06         | GET /jobs?limit=1.5                                                                                                                           | HTTP 400, code=1001                                                                                     |
| TC07         | GET /jobs?employment=PART_TIME                                                                                                                | HTTP 400, code=1001                                                                                     |
| TC08         | GET /jobs?companySizes=[1,"x"]                                                                                                                | HTTP 400, code=1001                                                                                     |
| TC09         | GET /jobs?employment=FULL_TIME&jobName=engineer                                                                                               | HTTP 200, code=0, returned jobs match FULL_TIME; jobName is matched by inclusion                        |
| TC10         | GET /jobs?companyName=Tech&industries=["AI"]&fundingTypes=[1,2]&filterSalaryMin=5000&filterSalaryMax=10000                                    | HTTP 200, code=0, filtered paginated result returned with valid response structure                      |


#### v3

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a formal software testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome

Clearly distinguish valid and invalid classes
Cover constraints: pagination defaults, pagination correction      rules, enum validation, type validation, and filtering behavior

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

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                 | Outcome                                                                 |
| ---- | --------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| EP1  | No query parameters provided                                                | Valid — HTTP 200, code=0, default page=1, limit=20                     |
| EP2  | page is a positive integer                                                  | Valid — HTTP 200                                                        |
| EP3  | page is 0 or negative integer                                               | Valid — corrected to 1, HTTP 200                                        |
| EP4  | page is non-integer or wrong type                                           | Invalid — HTTP 400, code=1001                                           |
| EP5  | limit is integer within 1..100                                              | Valid — HTTP 200                                                        |
| EP6  | limit is 0 or negative integer                                               | Valid — corrected to 20, HTTP 200                                     |
| EP7  | limit is greater than 100                                                     | Valid — truncated to 100, HTTP 200                                    |
| EP8  | limit is non-integer or wrong type                                          | Invalid — HTTP 400, code=1001                                           |
| EP9  | employment is INTERN or FULL_TIME                                           | Valid — HTTP 200                                                        |
| EP10 | employment is omitted                                                         | Valid — no employment restriction, HTTP 200                           |
| EP11 | employment is invalid enum or wrong type                                    | Invalid — HTTP 400, code=1001                                           |
| EP12 | companySizes is an array of integers                                        | Valid — HTTP 200                                                        |
| EP13 | companySizes is wrong type or contains non-integer values                    | Invalid — HTTP 400, code=1001                                           |
| EP14 | fundingTypes is an array of integers                                        | Valid — HTTP 200                                                        |
| EP15 | fundingTypes is wrong type or contains non-integer values                    | Invalid — HTTP 400, code=1001                                           |
| EP16 | industries is an array of strings                                           | Valid — HTTP 200                                                        |
| EP17 | industries is wrong type or contains non-string values                      | Invalid — HTTP 400, code=1001                                           |
| EP18 | companyName is a string                                                     | Valid — HTTP 200                                                        |
| EP19 | companyName is wrong type                                                   | Invalid — HTTP 400, code=1001                                           |
| EP20 | filterSalaryMin and filterSalaryMax are integers                            | Valid — HTTP 200                                                        |
| EP21 | filterSalaryMin or filterSalaryMax is wrong type                            | Invalid — HTTP 400, code=1001                                           |
| EP22 | jobName is a string                                                         | Valid — inclusion match applied, HTTP 200                               |
| EP23 | jobName is wrong type                                                       | Invalid — HTTP 400, code=1001                                           |
| EP24 | Multiple valid filters and pagination used together                         | Valid — HTTP 200, filtered paginated result                             |


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


| Test Case ID | Scenario                                                                                                                                      | Expected Result                                                                                                                            |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| TC01         | Send GET /jobs with no parameters                                                                                                             | HTTP 200, code=0; response contains data.list, data.total, data.page=1, data.limit=20, data.totalPages                                     |
| TC02         | Send GET /jobs?page=2&limit=50                                                                                                                | HTTP 200, code=0; response shows page=2, limit=50                                                                                          |
| TC03         | Send GET /jobs?page=0                                                                                                                         | HTTP 200, code=0; page is corrected to 1                                                                                                   |
| TC04         | Send GET /jobs?limit=0                                                                                                                        | HTTP 200, code=0; limit is corrected to 20                                                                                                 |
| TC05         | Send GET /jobs?limit=101                                                                                                                      | HTTP 200, code=0; limit is truncated to 100                                                                                                |
| TC06         | Send GET /jobs?page=abc                                                                                                                       | HTTP 400, code=1001                                                                                                                        |
| TC07         | Send GET /jobs?employment=PART_TIME                                                                                                           | HTTP 400, code=1001                                                                                                                        |
| TC08         | Send GET /jobs?companySizes=[1,"x"]                                                                                                           | HTTP 400, code=1001                                                                                                                        |
| TC09         | Send GET /jobs?employment=FULL_TIME&jobName=engineer                                                                                        | HTTP 200, code=0; results are restricted to FULL_TIME; jobName is matched by inclusion                                                  |
| TC10         | Send GET /jobs?page=-3&limit=150&companyName=Tech&industries=["AI"]&fundingTypes=[1,2]&filterSalaryMin=5000&filterSalaryMax=10000            | HTTP 200, code=0; page corrected to 1, limit truncated to 100; filtered paginated result returned with valid response structure           |


### 7.POST /jobs

**Input**

```text
System Overview:
The system under test is a RESTful job management API. The internal implementation is not visible. Testing is performed based on API specifications.

Functional Requirements:

R1. Create Job:
The system provides an API endpoint POST /jobs to create a new job record.

R2. Input Constraints:

jobName: required
companyName: required
description: required
location: required
salaryMin: required, integer
salaryMax: required, integer
salaryType: required, integer, allowed values: 1, 2, 3
link: required
companySize: optional, integer
companyFundingType: optional, integer
companyIndustries: optional, array of strings
companyIntroduction: optional, string
jobDuties: optional, array of strings
jobRequirements: optional, array of strings
keywords: optional, array of strings

R3. Business Rules:

Required fields must be provided
The endpoint creates a new job record when input is valid

R4. Error Handling:

Parameter validation failure (missing required fields or      invalid parameter/type) → HTTP 400, code 1001

R5. Authentication:

This endpoint requires bearer token authentication

R6. Success Response:

HTTP 200
code = 0
data      contains created job information
```

#### v1

**Prompt**

```text
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

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                                      | Outcome                         |
| ---- | ------------------------------------------------------------------------------------------------ | ------------------------------- |
| EP1  | All required fields provided with valid types and salaryType in {1,2,3}; optional fields omitted | Valid                           |
| EP2  | All required fields valid and optional fields also provided with valid types                     | Valid                           |
| EP3  | Missing jobName                                                                                  | Invalid → HTTP 400, code 1001   |
| EP4  | Missing companyName                                                                              | Invalid → HTTP 400, code 1001   |
| EP5  | Missing description                                                                              | Invalid → HTTP 400, code 1001   |
| EP6  | Missing location                                                                                 | Invalid → HTTP 400, code 1001   |
| EP7  | Missing salaryMin                                                                                | Invalid → HTTP 400, code 1001   |
| EP8  | Missing salaryMax                                                                                | Invalid → HTTP 400, code 1001   |
| EP9  | Missing salaryType                                                                               | Invalid → HTTP 400, code 1001   |
| EP10 | Missing link                                                                                     | Invalid → HTTP 400, code 1001   |
| EP11 | salaryMin is non-integer                                                                         | Invalid → HTTP 400, code 1001   |
| EP12 | salaryMax is non-integer                                                                         | Invalid → HTTP 400, code 1001   |
| EP13 | salaryType is non-integer                                                                        | Invalid → HTTP 400, code 1001   |
| EP14 | salaryType is integer but not in allowed set {1,2,3}                                             | Invalid → HTTP 400, code 1001   |
| EP15 | companySize provided as integer                                                                  | Valid                           |
| EP16 | companySize provided as non-integer                                                              | Invalid → HTTP 400, code 1001   |
| EP17 | companyFundingType provided as integer                                                           | Valid                           |
| EP18 | companyFundingType provided as non-integer                                                       | Invalid → HTTP 400, code 1001   |
| EP19 | companyIndustries, jobDuties, jobRequirements, keywords provided as arrays of strings            | Valid                           |
| EP20 | Any array field provided with wrong type or non-string elements                                  | Invalid → HTTP 400, code 1001   |
| EP21 | companyIntroduction provided as string                                                           | Valid                           |
| EP22 | companyIntroduction provided as non-string                                                       | Invalid → HTTP 400, code 1001   |
| EP23 | Request sent with valid bearer token                                                             | Valid                           |
| EP24 | Missing or invalid bearer token                                                                  | Authentication failure expected |


**2) Boundary Value Analysis**


| Field                                                                      | Boundary Values                                                                                  |
| -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| salaryType                                                                 | 0, 1, 2, 3, 4                                                                                    |
| salaryMin                                                                  | Smallest valid integer, 0, -1, very large integer, non-integer                                   |
| salaryMax                                                                  | Smallest valid integer, 0, -1, very large integer, non-integer                                   |
| companySize                                                                | Omitted, 0, -1, very large integer, non-integer                                                  |
| companyFundingType                                                         | Omitted, 0, -1, very large integer, non-integer                                                  |
| Required string fields (jobName, companyName, description, location, link) | Missing, empty string, 1-character string, very long string                                      |
| Optional string field (companyIntroduction)                                | Omitted, empty string, 1-character string, very long string, non-string                          |
| Array fields (companyIndustries, jobDuties, jobRequirements, keywords)     | Omitted, empty array, single-item array, multi-item array, array with non-string item, non-array |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                            | Expected Result                                                                   |
| ------------ | --------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| TC01         | Submit POST /jobs with all required fields valid, optional fields omitted, and valid bearer token   | HTTP 200, code=0, data contains created job information                           |
| TC02         | Submit POST /jobs with all required fields valid, all optional fields valid, and valid bearer token | HTTP 200, code=0, data contains created job information including optional fields |
| TC03         | Omit required field jobName while all other fields are valid                                        | HTTP 400, code=1001                                                               |
| TC04         | Omit multiple required fields such as companyName and link                                          | HTTP 400, code=1001                                                               |
| TC05         | Set salaryMin to a string value such as "5000" instead of integer                                   | HTTP 400, code=1001                                                               |
| TC06         | Set salaryMax to a decimal value such as 10000.5                                                    | HTTP 400, code=1001                                                               |
| TC07         | Set salaryType to boundary invalid value 0                                                          | HTTP 400, code=1001                                                               |
| TC08         | Set salaryType to boundary valid value 3 with other required fields valid                           | HTTP 200, code=0, data contains created job information                           |
| TC09         | Provide jobRequirements as ["Java", 123] or provide keywords as a string instead of array           | HTTP 400, code=1001                                                               |
| TC10         | Send valid request body but without bearer token authentication                                     | Authentication failure expected; request must not create job record               |


#### v2

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a structured testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome
Include both valid and invalid equivalence classes.

Boundary Value Analysis
Table columns: Field | Boundary Values
Include boundary values and representative values for important      fields such as salaryMin, salaryMax, and salaryType.

Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result

Requirements:

Cover valid inputs, missing required fields, invalid types,      invalid salaryType values, and authentication errors
Generate exactly 10 test cases
Keep      output concise and structured
```

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                                  | Outcome                        |
| ---- | -------------------------------------------------------------------------------------------- | ------------------------------ |
| EP01 | All required fields are present with valid data types; optional fields omitted               | Valid                          |
| EP02 | All required and optional fields are present with valid data types                           | Valid                          |
| EP03 | Any one required string field (jobName, companyName, description, location, link) is missing | Invalid → HTTP 400, code 1001  |
| EP04 | salaryMin is missing                                                                         | Invalid → HTTP 400, code 1001  |
| EP05 | salaryMax is missing                                                                         | Invalid → HTTP 400, code 1001  |
| EP06 | salaryType is missing                                                                        | Invalid → HTTP 400, code 1001  |
| EP07 | salaryMin or salaryMax is non-integer                                                        | Invalid → HTTP 400, code 1001  |
| EP08 | salaryType is non-integer                                                                    | Invalid → HTTP 400, code 1001  |
| EP09 | salaryType is integer but not in allowed set {1, 2, 3}                                       | Invalid → HTTP 400, code 1001  |
| EP10 | Optional integer fields (companySize, companyFundingType) provided as integers               | Valid                          |
| EP11 | Optional integer fields provided with invalid type                                           | Invalid → HTTP 400, code 1001  |
| EP12 | Optional array fields provided as arrays of strings                                          | Valid                          |
| EP13 | Optional array fields provided as non-array or containing non-string items                   | Invalid → HTTP 400, code 1001  |
| EP14 | Optional string field companyIntroduction provided as string                                 | Valid                          |
| EP15 | companyIntroduction provided with invalid type                                               | Invalid → HTTP 400, code 1001  |
| EP16 | Valid bearer token provided                                                                  | Valid                          |
| EP17 | Missing or invalid bearer token                                                              | Invalid → Authentication error |


**2) Boundary Value Analysis**


| Field                  | Boundary Values                                                             |
| ---------------------- | --------------------------------------------------------------------------- |
| salaryType             | 0, 1, 2, 3, 4                                                               |
| salaryMin              | -1, 0, 1, large integer (e.g. 2147483647), non-integer                      |
| salaryMax              | -1, 0, 1, large integer (e.g. 2147483647), non-integer                      |
| companySize            | omitted, -1, 0, 1, large integer, non-integer                               |
| companyFundingType     | omitted, -1, 0, 1, large integer, non-integer                               |
| Required string fields | missing, empty string, 1-character string, long string                      |
| Optional array fields  | omitted, empty array, 1 item, multiple items, non-string element, non-array |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                          | Expected Result                                           |
| ------------ | --------------------------------------------------------------------------------- | --------------------------------------------------------- |
| TC01         | Valid request with all required fields only and valid bearer token                | HTTP 200, code = 0, data contains created job information |
| TC02         | Valid request with all required fields plus all optional fields in correct format | HTTP 200, code = 0, data contains created job information |
| TC03         | Missing jobName while all other fields are valid                                  | HTTP 400, code = 1001                                     |
| TC04         | Missing salaryType while all other fields are valid                               | HTTP 400, code = 1001                                     |
| TC05         | salaryMin provided as string instead of integer                                   | HTTP 400, code = 1001                                     |
| TC06         | salaryMax provided as decimal instead of integer                                  | HTTP 400, code = 1001                                     |
| TC07         | salaryType = 0                                                                    | HTTP 400, code = 1001                                     |
| TC08         | salaryType = 4                                                                    | HTTP 400, code = 1001                                     |
| TC09         | jobRequirements contains a non-string element or keywords is not an array         | HTTP 400, code = 1001                                     |
| TC10         | Valid request body but bearer token is missing or invalid                         | Authentication error; job is not created                  |


#### v3

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a formal software testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome

Clearly distinguish valid and invalid classes
Cover constraints: required fields, type validation, enum      validation, and authentication

Boundary Value Analysis
Table columns: Field | Boundary Values

Include meaningful boundary values or representative values
salaryType: valid values 1, 2, 3 and invalid out-of-range      values
salaryMin and salaryMax: representative integer values and      invalid type cases

Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result

Requirements:

Use ONLY the provided requirements
Cover: normal case, missing required fields, invalid      salaryType, invalid parameter types, missing token, invalid token, and      optional fields present/absent
Generate exactly 10 high-quality test cases (no duplicates)
Each test case must correspond to a rule or boundary condition
Keep output concise, table-based, and suitable for a report
```

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                                                               | Outcome                        |
| ---- | ------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| EP01 | Required fields present with valid types; optional fields absent; valid bearer token                                      | Valid                          |
| EP02 | Required fields present with valid types; optional fields present with valid types; valid bearer token                    | Valid                          |
| EP03 | One or more required fields missing (jobName, companyName, description, location, salaryMin, salaryMax, salaryType, link) | Invalid → HTTP 400, code 1001  |
| EP04 | salaryMin or salaryMax provided with non-integer type                                                                     | Invalid → HTTP 400, code 1001  |
| EP05 | salaryType provided with non-integer type                                                                                 | Invalid → HTTP 400, code 1001  |
| EP06 | salaryType provided as integer outside allowed set {1, 2, 3}                                                              | Invalid → HTTP 400, code 1001  |
| EP07 | Optional integer fields (companySize, companyFundingType) present with valid integer values                               | Valid                          |
| EP08 | Optional integer fields present with invalid types                                                                        | Invalid → HTTP 400, code 1001  |
| EP09 | Optional array fields (companyIndustries, jobDuties, jobRequirements, keywords) present as arrays of strings              | Valid                          |
| EP10 | Optional array fields present as wrong type or contain non-string elements                                                | Invalid → HTTP 400, code 1001  |
| EP11 | Optional string field companyIntroduction present as string                                                               | Valid                          |
| EP12 | companyIntroduction present with non-string type                                                                          | Invalid → HTTP 400, code 1001  |
| EP13 | Missing bearer token                                                                                                      | Invalid → Authentication error |
| EP14 | Invalid bearer token                                                                                                      | Invalid → Authentication error |


**2) Boundary Value Analysis**


| Field                   | Boundary Values                                                                                                                    |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| salaryType              | Valid: 1, 2, 3; Invalid: 0, 4, non-integer ("1")                                                                                   |
| salaryMin               | Representative valid integers: 0, 1, 1000, 2147483647; Invalid type: "1000", 10.5                                                  |
| salaryMax               | Representative valid integers: 0, 1, 1000, 2147483647; Invalid type: "2000", 20.5                                                  |
| Required string fields  | Representative values: non-empty string, empty string, missing                                                                     |
| Optional integer fields | Representative values: absent, 0, 1, large integer; Invalid: string                                                                |
| Optional array fields   | Representative values: absent, empty array, one string item, multiple string items; Invalid: non-array, array with non-string item |
| Optional string field   | Representative values: absent, empty string, non-empty string; Invalid: non-string                                                 |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                   | Expected Result                                           |
| ------------ | -------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| TC01         | Submit POST /jobs with all required fields valid, optional fields absent, and valid bearer token                           | HTTP 200, code = 0, data contains created job information |
| TC02         | Submit POST /jobs with all required fields valid, all optional fields present with valid types, and valid bearer token     | HTTP 200, code = 0, data contains created job information |
| TC03         | Omit required field jobName; all other fields valid; valid bearer token                                                    | HTTP 400, code = 1001                                     |
| TC04         | Omit required field salaryType; all other fields valid; valid bearer token                                                 | HTTP 400, code = 1001                                     |
| TC05         | Set salaryType = 0; all other fields valid; valid bearer token                                                             | HTTP 400, code = 1001                                     |
| TC06         | Set salaryType = 4; all other fields valid; valid bearer token                                                             | HTTP 400, code = 1001                                     |
| TC07         | Set salaryMin to string value "5000" instead of integer; all other fields valid; valid bearer token                        | HTTP 400, code = 1001                                     |
| TC08         | Set optional field companyIndustries to "Software" instead of array of strings; all other fields valid; valid bearer token | HTTP 400, code = 1001                                     |
| TC09         | Submit valid request body without bearer token                                                                             | Authentication error; job record is not created           |
| TC10         | Submit valid request body with invalid bearer token                                                                        | Authentication error; job record is not created           |


### 8.PUT /jobs

**Input**

```text
System Overview:
The system under test is a RESTful job management API. The internal implementation is not visible. Testing is performed based on API specifications.

Functional Requirements:

R1. Update Job:
The system provides an API endpoint PUT /jobs to update an existing job record.

R2. Input Constraints:

id: required, integer
jobName: required
companyName: required
description: required
location: required
salaryMin: required, integer
salaryMax: required, integer
salaryType: required, integer, allowed values: 1, 2, 3
link: required
companySize: optional, integer
companyFundingType: optional, integer
companyIndustries: optional, array of strings
companyIntroduction: optional, string
jobDuties: optional, array of strings
jobRequirements: optional, array of strings
keywords: optional, array of strings

R3. Business Rules:

The job record must exist
Required fields must be provided
The endpoint updates the existing job record when input is      valid

R4. Error Handling:

Job does not exist → HTTP 404, code 8201
Parameter validation failure (missing required fields or      invalid parameter/type) → HTTP 400, code 1001

R5. Authentication:

This endpoint requires bearer token authentication

R6. Success Response:

HTTP 200
code = 0
data      contains updated job information
```

#### v1

**Prompt**

```text
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

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                                                                       | Outcome                        |
| ---- | --------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| EP01 | Existing id provided; all required fields valid; optional fields omitted; valid bearer token                                      | Valid                          |
| EP02 | Existing id provided; all required and optional fields valid; valid bearer token                                                  | Valid                          |
| EP03 | Missing required field id                                                                                                         | Invalid → HTTP 400, code 1001  |
| EP04 | id provided with non-integer type                                                                                                 | Invalid → HTTP 400, code 1001  |
| EP05 | id is valid format but job record does not exist                                                                                  | Invalid → HTTP 404, code 8201  |
| EP06 | Any required field other than id (jobName, companyName, description, location, salaryMin, salaryMax, salaryType, link) is missing | Invalid → HTTP 400, code 1001  |
| EP07 | salaryMin or salaryMax provided with non-integer type                                                                             | Invalid → HTTP 400, code 1001  |
| EP08 | salaryType provided with non-integer type                                                                                         | Invalid → HTTP 400, code 1001  |
| EP09 | salaryType provided as integer outside allowed set {1, 2, 3}                                                                      | Invalid → HTTP 400, code 1001  |
| EP10 | Optional integer fields (companySize, companyFundingType) provided as integers                                                    | Valid                          |
| EP11 | Optional integer fields provided with invalid type                                                                                | Invalid → HTTP 400, code 1001  |
| EP12 | Optional array fields (companyIndustries, jobDuties, jobRequirements, keywords) provided as arrays of strings                     | Valid                          |
| EP13 | Optional array fields provided as non-array or with non-string elements                                                           | Invalid → HTTP 400, code 1001  |
| EP14 | Optional string field companyIntroduction provided as string                                                                      | Valid                          |
| EP15 | companyIntroduction provided with non-string type                                                                                 | Invalid → HTTP 400, code 1001  |
| EP16 | Missing bearer token                                                                                                              | Invalid → Authentication error |
| EP17 | Invalid bearer token                                                                                                              | Invalid → Authentication error |


**2) Boundary Value Analysis**


| Field                                                                           | Boundary Values                                                                                     |
| ------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| id                                                                              | Missing, non-integer, 0, 1, large integer, non-existing valid integer                               |
| salaryType                                                                      | 0, 1, 2, 3, 4, non-integer                                                                          |
| salaryMin                                                                       | -1, 0, 1, large integer (e.g. 2147483647), non-integer                                              |
| salaryMax                                                                       | -1, 0, 1, large integer (e.g. 2147483647), non-integer                                              |
| Required string fields (jobName, companyName, description, location, link)      | Missing, empty string, 1-character string, long string                                              |
| Optional integer fields (companySize, companyFundingType)                       | Omitted, -1, 0, 1, large integer, non-integer                                                       |
| Optional array fields (companyIndustries, jobDuties, jobRequirements, keywords) | Omitted, empty array, single-item array, multi-item array, non-array, array with non-string element |
| Optional string field (companyIntroduction)                                     | Omitted, empty string, 1-character string, long string, non-string                                  |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                        | Expected Result                                           |
| ------------ | --------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| TC01         | Submit PUT /jobs with existing id, all required fields valid, optional fields omitted, and valid bearer token   | HTTP 200, code = 0, data contains updated job information |
| TC02         | Submit PUT /jobs with existing id, all required fields valid, all optional fields valid, and valid bearer token | HTTP 200, code = 0, data contains updated job information |
| TC03         | Omit required field id while all other fields are valid                                                         | HTTP 400, code = 1001                                     |
| TC04         | Provide id as string instead of integer while all other fields are valid                                        | HTTP 400, code = 1001                                     |
| TC05         | Provide a valid integer id that does not correspond to any existing job record                                  | HTTP 404, code = 8201                                     |
| TC06         | Omit required field jobName while all other fields are valid and id exists                                      | HTTP 400, code = 1001                                     |
| TC07         | Set salaryMin to a string such as "5000" instead of integer                                                     | HTTP 400, code = 1001                                     |
| TC08         | Set salaryType to invalid enum value 4 while all other fields are valid and id exists                           | HTTP 400, code = 1001                                     |
| TC09         | Submit a valid request body with existing id but without bearer token                                           | Authentication error; job record is not updated           |
| TC10         | Submit a valid request body with existing id but with invalid bearer token                                      | Authentication error; job record is not updated           |


#### v2

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a structured testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome
Include both valid and invalid equivalence classes.

Boundary Value Analysis
Table columns: Field | Boundary Values
Include boundary values and representative values for important      fields such as id, salaryMin, salaryMax, and salaryType.

Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result

Requirements:

Cover valid inputs, missing required fields, invalid types,      invalid salaryType values, non-existing job, and authentication errors
Generate exactly 10 test cases
Keep      output concise and structured
```

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                                                                       | Outcome                        |
| ---- | --------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| EP01 | Existing id provided; all required fields valid; optional fields omitted; valid bearer token                                      | Valid                          |
| EP02 | Existing id provided; all required and optional fields valid; valid bearer token                                                  | Valid                          |
| EP03 | Missing required field id                                                                                                         | Invalid → HTTP 400, code 1001  |
| EP04 | id provided with non-integer type                                                                                                 | Invalid → HTTP 400, code 1001  |
| EP05 | Valid integer id provided, but job record does not exist                                                                          | Invalid → HTTP 404, code 8201  |
| EP06 | Any required field other than id is missing (jobName, companyName, description, location, salaryMin, salaryMax, salaryType, link) | Invalid → HTTP 400, code 1001  |
| EP07 | salaryMin or salaryMax has invalid type (non-integer)                                                                             | Invalid → HTTP 400, code 1001  |
| EP08 | salaryType has invalid type (non-integer)                                                                                         | Invalid → HTTP 400, code 1001  |
| EP09 | salaryType is integer but outside allowed values {1, 2, 3}                                                                        | Invalid → HTTP 400, code 1001  |
| EP10 | Optional integer fields (companySize, companyFundingType) provided as integers                                                    | Valid                          |
| EP11 | Optional integer fields provided with invalid type                                                                                | Invalid → HTTP 400, code 1001  |
| EP12 | Optional array fields (companyIndustries, jobDuties, jobRequirements, keywords) provided as arrays of strings                     | Valid                          |
| EP13 | Optional array fields provided as non-array or containing non-string elements                                                     | Invalid → HTTP 400, code 1001  |
| EP14 | Optional string field companyIntroduction provided as string                                                                      | Valid                          |
| EP15 | companyIntroduction provided as non-string                                                                                        | Invalid → HTTP 400, code 1001  |
| EP16 | Missing bearer token                                                                                                              | Invalid → Authentication error |
| EP17 | Invalid bearer token                                                                                                              | Invalid → Authentication error |


**2) Boundary Value Analysis**


| Field                   | Boundary Values                                                                                        |
| ----------------------- | ------------------------------------------------------------------------------------------------------ |
| id                      | Missing, non-integer, 0, 1, large integer, valid but non-existing integer                              |
| salaryType              | Valid: 1, 2, 3; Invalid: 0, 4, non-integer                                                             |
| salaryMin               | Valid representative integers: 0, 1, 1000, 2147483647; Invalid: "1000", 10.5                           |
| salaryMax               | Valid representative integers: 0, 1, 1000, 2147483647; Invalid: "2000", 20.5                           |
| Required string fields  | Missing, empty string, 1-character string, long string                                                 |
| Optional integer fields | Omitted, 0, 1, large integer, non-integer                                                              |
| Optional array fields   | Omitted, empty array, one string item, multiple string items, non-array, array with non-string element |
| Optional string field   | Omitted, empty string, non-empty string, non-string                                                    |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                                  | Expected Result                                           |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| TC01         | Valid PUT /jobs request with existing id, all required fields valid, optional fields omitted, and valid bearer token                      | HTTP 200, code = 0, data contains updated job information |
| TC02         | Valid PUT /jobs request with existing id, all required fields valid, all optional fields present with valid types, and valid bearer token | HTTP 200, code = 0, data contains updated job information |
| TC03         | Omit required field id while all other inputs are valid                                                                                   | HTTP 400, code = 1001                                     |
| TC04         | Provide id as a string instead of integer                                                                                                 | HTTP 400, code = 1001                                     |
| TC05         | Provide valid integer id for a non-existing job record                                                                                    | HTTP 404, code = 8201                                     |
| TC06         | Omit required field jobName while other fields are valid and id exists                                                                    | HTTP 400, code = 1001                                     |
| TC07         | Provide salaryMin as string "5000" instead of integer                                                                                     | HTTP 400, code = 1001                                     |
| TC08         | Provide salaryType = 4 with all other fields valid and id exists                                                                          | HTTP 400, code = 1001                                     |
| TC09         | Submit valid request body for existing id without bearer token                                                                            | Authentication error; job record is not updated           |
| TC10         | Submit valid request body for existing id with invalid bearer token                                                                       | Authentication error; job record is not updated           |


#### v3

**Prompt**

```text
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate the result in a formal software testing report format with three sections:

Equivalence Partitioning
Table columns: ID | Description | Outcome

Clearly distinguish valid and invalid classes
Cover constraints: required id, required fields, type      validation, enum validation, resource existence, and authentication

Boundary Value Analysis
Table columns: Field | Boundary Values

Include meaningful boundary values or representative values
id: existing, non-existing, invalid type
salaryType: valid values 1, 2, 3 and invalid out-of-range      values
salaryMin and salaryMax: representative integer values and      invalid type cases

Sample Test Cases
Table columns: Test Case ID | Scenario | Expected Result

Requirements:

Use ONLY the provided requirements
Cover: normal update, missing id, missing required fields,      invalid salaryType, invalid parameter types, non-existing job, missing      token, invalid token, and optional fields present/absent
Generate exactly 10 high-quality test cases (no duplicates)
Each test case must correspond to a rule or boundary condition
Keep output concise, table-based, and suitable for a report
```

**Output**

**1) Equivalence Partitioning**


| ID   | Description                                                                                                                                 | Outcome                        |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| EP01 | Existing id provided; all required fields valid; optional fields absent; valid bearer token                                                 | Valid                          |
| EP02 | Existing id provided; all required fields valid; optional fields present with valid types; valid bearer token                               | Valid                          |
| EP03 | Required id is missing                                                                                                                      | Invalid → HTTP 400, code 1001  |
| EP04 | id provided with invalid type (non-integer)                                                                                                 | Invalid → HTTP 400, code 1001  |
| EP05 | id is a valid integer but does not correspond to an existing job record                                                                     | Invalid → HTTP 404, code 8201  |
| EP06 | One or more required fields other than id (jobName, companyName, description, location, salaryMin, salaryMax, salaryType, link) are missing | Invalid → HTTP 400, code 1001  |
| EP07 | salaryMin or salaryMax provided with invalid type (non-integer)                                                                             | Invalid → HTTP 400, code 1001  |
| EP08 | salaryType provided with invalid type (non-integer)                                                                                         | Invalid → HTTP 400, code 1001  |
| EP09 | salaryType provided as integer outside allowed values {1, 2, 3}                                                                             | Invalid → HTTP 400, code 1001  |
| EP10 | Optional integer fields (companySize, companyFundingType) provided as integers                                                              | Valid                          |
| EP11 | Optional integer fields provided with invalid type                                                                                          | Invalid → HTTP 400, code 1001  |
| EP12 | Optional array fields (companyIndustries, jobDuties, jobRequirements, keywords) provided as arrays of strings                               | Valid                          |
| EP13 | Optional array fields provided as non-array or contain non-string elements                                                                  | Invalid → HTTP 400, code 1001  |
| EP14 | Optional string field companyIntroduction provided as string                                                                                | Valid                          |
| EP15 | companyIntroduction provided with invalid type                                                                                              | Invalid → HTTP 400, code 1001  |
| EP16 | Bearer token is missing                                                                                                                     | Invalid → Authentication error |
| EP17 | Bearer token is invalid                                                                                                                     | Invalid → Authentication error |


**2) Boundary Value Analysis**


| Field                   | Boundary Values                                                                               |
| ----------------------- | --------------------------------------------------------------------------------------------- |
| id                      | Existing valid integer, non-existing valid integer, missing, non-integer ("1")                |
| salaryType              | Valid: 1, 2, 3; Invalid: 0, 4, "1"                                                            |
| salaryMin               | Representative valid integers: 0, 1, 1000, 2147483647; Invalid: "1000", 10.5                  |
| salaryMax               | Representative valid integers: 0, 1, 1000, 2147483647; Invalid: "2000", 20.5                  |
| Required string fields  | Present with valid string, missing                                                            |
| Optional integer fields | Absent, valid integer, invalid type                                                           |
| Optional array fields   | Absent, empty array, one string item, multiple string items, invalid type, non-string element |
| Optional string field   | Absent, valid string, invalid type                                                            |


**3) Sample Test Cases**


| Test Case ID | Scenario                                                                                                                           | Expected Result                                           |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| TC01         | Submit PUT /jobs with existing id, all required fields valid, optional fields absent, and valid bearer token                       | HTTP 200, code = 0, data contains updated job information |
| TC02         | Submit PUT /jobs with existing id, all required fields valid, all optional fields present with valid types, and valid bearer token | HTTP 200, code = 0, data contains updated job information |
| TC03         | Omit required field id; all other fields valid; valid bearer token                                                                 | HTTP 400, code = 1001                                     |
| TC04         | Omit required field jobName; existing id and all other fields valid; valid bearer token                                            | HTTP 400, code = 1001                                     |
| TC05         | Provide id as string instead of integer; all other fields valid; valid bearer token                                                | HTTP 400, code = 1001                                     |
| TC06         | Provide non-existing valid integer id; all other fields valid; valid bearer token                                                  | HTTP 404, code = 8201                                     |
| TC07         | Provide salaryMin as string "5000" instead of integer; existing id; other fields valid; valid bearer token                         | HTTP 400, code = 1001                                     |
| TC08         | Provide salaryType = 4; existing id; all other fields valid; valid bearer token                                                    | HTTP 400, code = 1001                                     |
| TC09         | Submit valid request body for existing id without bearer token                                                                     | Authentication error; job record is not updated           |
| TC10         | Submit valid request body for existing id with invalid bearer token                                                                | Authentication error; job record is not updated           |


状态迁移测试

## **Input**
```
System Overview:

The system under test is a RESTful user management API. Testing is performed based on API specifications.

Relevant Endpoints:

- POST /users - Register (no auth required)
- POST /users/login - Login (no auth required)
- GET /users/{id} - Get user by ID (auth required)
- PUT /users/me - Update user info (auth required)

State Definition:

- S1: Unregistered
- S2: Registered but not logged in
- S3: Logged in (token valid)
- S4: Token expired

Valid Transitions:

- S1 → S2: POST /users (successful registration)
- S2 → S3: POST /users/login (successful login)
- S3 → S4: token expiry

Invalid Transitions (should fail):

- S1 → S3: call authenticated endpoint without login
- S2 → S3: call authenticated endpoint with invalid credentials
```
## **v1**

### **Prompt**
```
You are a software testing expert.

Given the following system overview and requirements:

{Input}

Generate state transition test cases for the user authentication flow.

Output a table with columns: Test Case ID | Initial State | Action | Expected Final State | Expected Result

Generate exactly 5 test cases.
```
### **Output**


| Test Case ID | Initial State                     | Action                                                    | Expected Final State              | Expected Result                                                                              |
| ------------ | --------------------------------- | --------------------------------------------------------- | --------------------------------- | -------------------------------------------------------------------------------------------- |
| TC1          | S1 (Unregistered)                 | POST /users with valid new user data                      | S2 (Registered but not logged in) | 201 Created; user is registered but no token returned; cannot access authenticated endpoints |
| TC2          | S2 (Registered but not logged in) | POST /users/login with valid credentials                  | S3 (Logged in, token valid)       | 200 OK; authentication token returned and valid                                              |
| TC3          | S3 (Logged in)                    | Wait for token expiry (simulated via time or mock expiry) | S4 (Token expired)                | 401 Unauthorized when trying to use expired token                                            |
| TC4          | S1 (Unregistered)                 | GET /users/{id} (authenticated endpoint) without token    | S1 (Unregistered)                 | 401 Unauthorized; user remains unregistered                                                  |
| TC5          | S2 (Registered but not logged in) | POST /users/login with invalid credentials                | S2 (Registered but not logged in) | 401 Unauthorized; no token issued; user still not logged in                                  |


## **v2**

### **Prompt**
```
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate state transition test cases for the user authentication flow.

Requirements:

- Cover valid transitions: registration → login → token expiry
- Cover invalid transitions: unregistered user calling authenticated endpoint, login with invalid credentials
- Include HTTP status codes and business codes in expected results

Output a table with columns: Test Case ID | Initial State | Action | Expected Final State | Expected HTTP Status | Expected Business Code

Generate exactly 8 test cases.
```
### **Output**


| Test Case ID | Initial State                     | Action                                           | Expected Final State              | Expected HTTP Status             | Expected Business Code                                          |
| ------------ | --------------------------------- | ------------------------------------------------ | --------------------------------- | -------------------------------- | --------------------------------------------------------------- |
| TC1          | S1 (Unregistered)                 | POST /users with valid unique email and password | S2 (Registered but not logged in) | 201 Created                      | REG_SUCCESS (e.g., 1000)                                        |
| TC2          | S2 (Registered but not logged in) | POST /users/login with correct credentials       | S3 (Logged in, token valid)       | 200 OK                           | LOGIN_SUCCESS (e.g., 1100)                                      |
| TC3          | S3 (Logged in)                    | Wait for token TTL expiry (e.g., 30 min)         | S4 (Token expired)                | 200 OK (on expiry action) or N/A | TOKEN_EXPIRED (e.g., 1301) when next authenticated call is made |
| TC4          | S4 (Token expired)                | PUT /users/me with expired token                 | S4 (Token expired)                | 401 Unauthorized                 | TOKEN_EXPIRED (e.g., 1301)                                      |
| TC5          | S1 (Unregistered)                 | GET /users/{id} without any auth header          | S1 (Unregistered)                 | 401 Unauthorized                 | AUTH_MISSING (e.g., 1300)                                       |
| TC6          | S2 (Registered but not logged in) | POST /users/login with wrong password            | S2 (Registered but not logged in) | 401 Unauthorized                 | INVALID_CREDENTIALS (e.g., 1101)                                |
| TC7          | S2 (Registered but not logged in) | GET /users/{id} with missing or invalid token    | S2 (Registered but not logged in) | 401 Unauthorized                 | AUTH_MISSING (e.g., 1300)                                       |
| TC8          | S1 (Unregistered)                 | POST /users/login with non-existent email        | S1 (Unregistered)                 | 401 Unauthorized                 | USER_NOT_FOUND (e.g., 1102)                                     |


## **v3**

### **Prompt**

```
You are a software testing expert performing black-box testing.

Given the following system overview and requirements:

{Input}

Generate state transition test cases for the user authentication flow.

State Definition:

- S1: Unregistered
- S2: Registered but not logged in
- S3: Logged in (has valid token)
- S4: Token expired

Valid Transitions:

- S1 → S2: POST /users (successful registration)
- S2 → S3: POST /users/login (successful login)
- S3 → S4: wait for token expiry or simulate expired token

Invalid Transitions (should return 401):

- S1 → S3: call GET /users/1 without logging in
- S2 → S3: call GET /users/1 with invalid token
- S3 → S2: call authenticated endpoint with expired token

Requirements:

- Use ONLY the provided requirements
- Cover all valid and invalid transitions listed above
- Each test case must correspond to one transition
- Include concrete API call examples in the Action column

Output a table with columns:

Test Case ID | Initial State | Action (API call example) | Expected Final State | Expected HTTP Status | Expected Business Code

Generate exactly 8-10 test cases.
```

### **Output**


| TC ID | Initial State                  | Action (API call example)                                            | Expected Final State           | Expected HTTP | Expected Code |
| ----- | ------------------------------ | -------------------------------------------------------------------- | ------------------------------ | ------------- | ------------- |
| TC1   | S1 (Unregistered)              | `POST /users` with valid username, password, email, verificationCode | S2 (Registered, not logged in) | 200           | 0             |
| TC2   | S2 (Registered, not logged in) | `POST /users/login` with valid credentials                           | S3 (Logged in, token valid)    | 200           | 0             |
| TC3   | S3 (Logged in)                 | `GET /users/1` with expired token                                    | S2 (Not logged in)             | 401           | 401           |
| TC4   | S1 (Unregistered)              | `GET /users/1` without token                                         | S1 (Unregistered)              | 401           | 401           |
| TC5   | S2 (Registered, not logged in) | `GET /users/1` with invalid token (e.g., "abc123")                   | S2 (Not logged in)             | 401           | 401           |
| TC6   | S3 (Logged in)                 | `GET /users/1` with expired token                                    | S2 (Not logged in)             | 401           | 401           |
| TC7   | S2 (Registered, not logged in) | `POST /users/login` with wrong password                              | S2 (Not logged in)             | 400           | 2005          |
| TC8   | S2 (Registered, not logged in) | `POST /users/login` with non-existent email                          | S2 (Not logged in)             | 400           | 2005          |
| TC9   | S1 (Unregistered)              | `PUT /users/me` without token                                        | S1 (Unregistered)              | 401           | 401           |
| TC10  | S3 (Logged in)                 | `PUT /users/me` with valid token                                     | S3 (Logged in)                 | 200           | 0             |


