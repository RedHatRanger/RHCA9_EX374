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
