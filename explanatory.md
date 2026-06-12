# Beginner Explanatory Guide: PLATFORM-2970: Fix Blue-Green Deployment Health Check Race

> **Task Type**: Product Task  
> **Domain/Focus**: Python fundamentals, Deployment Strategies

---

## 1. The Goal (In-Depth Beginner Explanation)

### The Core Problem
The task at hand addresses a critical issue in the deployment process of a software application using a blue-green deployment strategy. In this approach, two identical environments (blue and green) are maintained, allowing for seamless transitions between versions of the application. The current implementation has a significant flaw: it switches traffic to the new version before confirming that it is healthy and functioning correctly. This premature switch can lead to users experiencing errors or downtime if the new version fails health checks after the switch has occurred.

Fixing this issue is crucial because it directly impacts user experience and system reliability. If the application fails after the traffic switch, users may encounter broken features or downtime, leading to frustration and loss of trust in the application. Moreover, the absence of an automatic rollback mechanism means that if the health checks fail, there is no immediate way to revert to the previous stable version, exacerbating the problem. Therefore, implementing a solution that ensures all health checks pass before switching traffic and includes an automatic rollback feature is essential for maintaining a robust deployment process.

### Jargon Buster (Key Terms Explained)
* **Blue-Green Deployment**: This is a deployment strategy that uses two identical environments (blue and green). At any time, one environment is live (serving users), while the other is idle (ready for the next release). This allows for quick rollbacks and minimal downtime. For example, if the blue environment is live and a new version is deployed to the green environment, traffic can be switched to green only after successful health checks.

* **Health Check**: A health check is a process that verifies whether a deployed application is functioning correctly. It typically involves sending requests to the application and checking for expected responses. For instance, if a health check for a web application checks if the homepage returns a 200 OK status, a failure would indicate that the application is not healthy.

* **Rollback**: A rollback is the process of reverting an application to a previous stable state after a deployment fails. This is crucial in deployment strategies to ensure that users are not affected by faulty updates. For example, if a new version of an application causes errors, the system can roll back to the last known good version to restore functionality.

* **Traffic Split**: This refers to the gradual shifting of user traffic from one version of an application to another. Instead of switching all traffic at once, a traffic split allows for a percentage of users to be directed to the new version while the rest continue using the old version. For example, initially directing 10% of traffic to the new version allows for monitoring before a full switch.

### Expected Outcome
After implementing the solution, the deployment process should behave as follows:

**Before**: The system switches traffic to the new version immediately after deployment, regardless of whether the health checks pass. If the health checks fail, users may experience errors, and there is no automatic rollback.

**After**: The system waits for all health checks to pass before switching traffic to the new version. If any health check fails within a specified time frame (5 minutes), the system automatically rolls back to the previous version, ensuring that users are not affected by faulty deployments.

---

## 2. Related Coding Concepts & Syntax (50% Theory, 50% Practice)

### Concept 1: Functions and Method Calls
#### 📘 Theoretical Overview (50%)
Functions are reusable blocks of code that perform a specific task. They help organize code, making it more readable and maintainable. In Python, functions can take inputs (parameters) and return outputs. When a function is defined within a class, it is referred to as a method. Understanding how to define and call functions is essential for implementing the solution, as we will need to create methods for health checks and rollback processes.

Key mechanisms include:
- **Defining a Function**: Using the `def` keyword followed by the function name and parameters.
- **Calling a Function**: Using the function name followed by parentheses, optionally including arguments.
- **Return Statement**: The `return` keyword is used to send back a value from the function.

#### 💻 Syntax & Practical Examples (50%)
* **Language Syntax**:
  ```python
  def function_name(parameter1, parameter2):
      """This is a docstring explaining the function."""
      # Function logic here
      return result
  ```

* **Real-World Application**:
  ```python
  def add_numbers(a, b):
      """Returns the sum of two numbers."""
      return a + b

  result = add_numbers(5, 3)  # Calling the function
  print(result)  # Output: 8
  ```

### Concept 2: Conditional Statements
#### 📘 Theoretical Overview (50%)
Conditional statements allow the program to execute certain blocks of code based on whether a condition is true or false. This is crucial for implementing logic such as health checks and rollback mechanisms. The most common conditional statement in Python is the `if` statement, which can be combined with `elif` (else if) and `else` to create complex decision-making structures.

Key mechanisms include:
- **If Statement**: Executes a block of code if the condition is true.
- **Elif Statement**: Checks another condition if the previous `if` was false.
- **Else Statement**: Executes a block of code if all previous conditions were false.

#### 💻 Syntax & Practical Examples (50%)
* **Language Syntax**:
  ```python
  if condition:
      # Code to execute if condition is true
  elif another_condition:
      # Code to execute if the previous condition is false and this one is true
  else:
      # Code to execute if all previous conditions are false
  ```

* **Real-World Application**:
  ```python
  health_check_passed = True

  if health_check_passed:
      print("Deployment successful!")
  else:
      print("Deployment failed, rolling back.")
  ```

---

## 3. Step-by-Step Logic & Walkthrough

1. **Step 1: Locate and Analyze the Target File**
   * Navigate to the `p-w08-hotfix-01` folder and open the `deployManager.py` file. This file contains the class `BlueGreenDeployer`, which is responsible for managing the deployment process.
   * Focus on the `deploy` method, particularly the lines where traffic is switched and health checks are performed.

2. **Step 2: Input Verification & Validation**
   * Before making changes, ensure that the `new_version` parameter passed to the `deploy` method is valid (not null or empty). This can be done by adding a check at the beginning of the method.

3. **Step 3: Core Implementation / Modification**
   * Modify the `deploy` method to first run health checks before calling `self.switch_traffic()`. This ensures that traffic is only switched if the health checks pass.
   * Implement an automatic rollback mechanism by calling the `rollback` method if health checks fail.

4. **Step 4: Output Verification & Testing**
   * After making the changes, run the tests included at the bottom of the `deployManager.py` file. Ensure that the assertions pass and that the output reflects the expected behavior after a deployment.

---

## 4. Detailed Walkthrough of Test Cases

### Test Case 1: Standard / Success Case
* **Description**: This test represents a successful deployment where health checks pass.
* **Inputs**:
  ```json
  {
      "new_version": "v2.0.0"
  }
  ```
* **Step-by-Step Execution Trace**:
  1. The `deploy` method is called with `new_version` set to "v2.0.0".
  2. The method runs health checks for the "green" environment.
  3. The health checks return `True`, indicating that the new version is healthy.
  4. The traffic is switched to the "green" environment.
* **Expected Output**: The output should indicate that the deployment was successful, and the active environment should now be "green".

### Test Case 2: Edge Case / Validation Fail
* **Description**: This test represents a scenario where health checks fail after deployment.
* **Inputs**:
  ```json
  {
      "new_version": "v2.0.0"
  }
  ```
* **Step-by-Step Execution Trace**:
  1. The `deploy` method is called with `new_version` set to "v2.0.0".
  2. The method runs health checks for the "green" environment.
  3. The health checks return `False`, indicating that the new version is not healthy.
  4. The `rollback` method is called, switching back to the "blue" environment.
* **Expected Output**: The output should indicate that health checks failed and that the system rolled back to the "blue" environment. The active environment should remain "blue".