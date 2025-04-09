**Lesson 10 Summary: Controlling Task Execution with Conditionals (Simple Version)**

---

### ✅ Objectives Checklist (EX374):
- [ ] Use `when` to run tasks only when a condition is true
- [ ] Handle undefined variables with `default` or `is defined`
- [ ] Use logical operators (and, or, not) in conditions
- [ ] Apply conditionals inside loops
- [ ] Test values in lists or match patterns

---

### 🧠 What You Learn in This Chapter:
This chapter teaches you how to control when a task should run in your playbook. You’ll use the `when` keyword to make sure tasks only run when needed.

You also learn how to handle variables that may not be defined, and how to build smarter conditions using **filters** and **logic**.

---

### 🎯 Why It Matters for the EX374 Exam:
- You need to be able to control task flow using conditions
- The exam may ask you to skip or include tasks based on facts or variable values
- Using `when`, `default`, and `is defined` correctly can prevent errors

---

### ✅ Easy Terms:
- **when**: A keyword that tells Ansible to run a task only if the condition is true
- **is defined**: A check to see if a variable exists before using it
- **default**: A backup value used when a variable is missing
- **Logical Operators**: Words like `and`, `or`, and `not` to combine conditions

---

### 📘 Examples:
#### Basic `when` condition:
```yaml
- name: Restart Apache only if it’s installed
  service:
    name: httpd
    state: restarted
  when: ansible_facts['pkg_mgr'] == 'yum'
```

#### Check if variable exists:
```yaml
- name: Only run if the app_name variable is defined
  debug:
    msg: "Deploying {{ app_name }}"
  when: app_name is defined
```

#### Use a fallback value:
```yaml
- name: Use default app name if not set
  debug:
    msg: "Starting {{ app_name | default('MyApp') }}"
```

#### Use `when` in a loop:
```yaml
- name: Only print names with an "a"
  debug:
    msg: "{{ item }} has an 'a' in it"
  loop:
    - bob
    - anna
    - eli
  when: "a" in item
```

---

### 💻 Helpful Commands:
```bash
# Run a playbook using conditionals and variables
ansible-navigator run site.yml -i inventory/hosts.yml -m stdout
```

---

### 📦 What You Should Be Able to Do:
- Use `when` to control tasks
- Avoid errors with `default` and `is defined`
- Combine conditions using logic
- Apply conditionals inside loops

---

This chapter helps you write **smarter playbooks that do the right thing at the right time**!

<br><br><br><br>
---

**Lesson 10 Quiz: Controlling Task Execution with Conditionals**

---

**1. What keyword is used in Ansible to evaluate whether a task should run based on a condition?**

a) check  
b) only_if  
c) when  
d) condition  
**Answer: c**

---

**2. What data structure must the expression in a `when` condition evaluate to?**

a) string  
b) boolean  
c) list  
d) integer  
**Answer: b**

---

**3. How would you check if a variable `my_var` equals `"yes"` in a task?**

a) when: my_var is "yes"  
b) when: my_var == "yes"  
c) when: "my_var" equals yes  
d) when: is(my_var, yes)  
**Answer: b**

---

**4. What happens if a variable used in a `when` clause is undefined and not handled?**

a) The task is skipped silently  
b) An error occurs unless the variable is explicitly defined or handled  
c) Ansible sets it to null  
d) It defaults to false and skips the task  
**Answer: b**

---

**5. How can you prevent errors when referencing a possibly undefined variable in a `when` condition?**

a) Use the `default` filter  
b) Use `assert` before the task  
c) Quote the variable name  
d) Set `check_mode`  
**Answer: a**

---

**6. What is the result of the expression `my_var is defined` in a `when` clause?**

a) True if `my_var` is not null  
b) True if `my_var` is declared  
c) True if `my_var` has been registered  
d) True only for global variables  
**Answer: b**

---

**7. Which Jinja2 filter is commonly used with `when` to check list or string contents?**

a) contains  
b) has  
c) in  
d) match  
**Answer: c**

---

**8. How do you test whether a variable is defined and equals `true`?**

a) when: my_var and my_var == true  
b) when: my_var is defined and my_var  
c) when: defined(my_var) == true  
d) when: my_var if defined  
**Answer: b**

---

**9. What is the advantage of using `set_fact` to simplify complex `when` expressions?**

a) It allows you to avoid using variables  
b) It lets you use roles inside conditions  
c) It breaks long conditionals into reusable variables  
d) It removes the need for filters  
**Answer: c**

---

**10. In a `when` clause, how would you evaluate a condition based on an item inside a loop?**

a) when: item.value == 5  
b) when: item is equal to 5  
c) when: with_items[item] == 5  
d) when: loop_item = 5  
**Answer: a**
