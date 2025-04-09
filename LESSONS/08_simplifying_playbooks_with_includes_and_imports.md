**Lesson 8 Summary: Simplifying Playbooks with Includes and Imports (Simple Version)**

---

### ✅ Objectives Checklist (EX374):
- [ ] Use `import_tasks` and `include_tasks` to reuse task files
- [ ] Understand when includes are processed (runtime vs. parse time)
- [ ] Use `import_playbook` to combine playbooks
- [ ] Use includes conditionally or in loops
- [ ] Keep playbooks clean and organized using includes/imports

---

### 🧠 What You Learn in This Chapter:
This chapter shows you how to keep your playbooks short and neat by moving tasks into smaller files and pulling them in with `import` and `include` commands.

You’ll learn how and when to use `import_tasks`, `include_tasks`, and `import_playbook`. You’ll also learn when Ansible processes each one (at the beginning or during the run).

---

### 🎯 Why It Matters for the EX374 Exam:
- These tools are used to break up large playbooks into smaller, reusable pieces
- The exam may test whether you understand how conditional logic works with includes vs imports
- Organizing tasks cleanly is key to writing good automation code

---

### ✅ Easy Terms:
- **import_tasks**: Pulls in a file of tasks before the play runs (parse-time)
- **include_tasks**: Pulls in tasks while the play is running (runtime)
- **import_playbook**: Pulls in an entire playbook file before the play runs

---

### 📘 Example:
Let’s say you have a playbook that installs software, configures it, and starts a service. You can split these into three files:

- `install.yml`
- `configure.yml`
- `start.yml`

Now your main playbook can look like this:
```yaml
- name: Setup App
  hosts: node1.lab.example.com
  become: true
  tasks:
    - import_tasks: install.yml
    - import_tasks: configure.yml
    - import_tasks: start.yml
```

Or if you want to use conditions or loops:
```yaml
- name: Conditional Setup
  hosts: node1.lab.example.com
  become: true
  tasks:
    - include_tasks: install.yml
      when: install_app
```

---

### 💻 Helpful Commands:
```bash
# Check syntax of a playbook that uses includes/imports
ansible-navigator run main.yml --syntax-check

# Run a playbook that uses import_playbook
ansible-navigator run site.yml -m stdout
```

---

### 📦 What You Should Be Able to Do:
- Clean up big playbooks by splitting them into smaller files
- Choose between import and include based on when you need it to run
- Combine playbooks with import_playbook
- Use conditional includes safely and correctly

---

This chapter is all about making your automation files **easy to read, reuse, and manage**!

<br><br><br><br>
---

**Lesson 8 Quiz: Simplifying Playbooks with Includes and Imports**

---

**1. What is the primary benefit of using includes and imports in Ansible playbooks?**

a) They allow version control over modules  
b) They reduce YAML syntax errors  
c) They help reuse and organize tasks, handlers, and roles  
d) They compile tasks for faster execution  
**Answer: c**

---

**2. What is the difference between `import_tasks` and `include_tasks`?**

a) `import_tasks` is conditional; `include_tasks` is static  
b) `import_tasks` is resolved at playbook parsing time; `include_tasks` is resolved at runtime  
c) `include_tasks` supports tags; `import_tasks` does not  
d) There is no difference  
**Answer: b**

---

**3. When would you prefer to use `include_tasks` instead of `import_tasks`?**

a) When including tasks inside a loop or conditionally  
b) When importing static files  
c) When defining play-level variables  
d) When reusing inventory files  
**Answer: a**

---

**4. What is the behavior of `import_playbook`?**

a) Imports a role into the controller  
b) Loads a playbook file statically during playbook parsing  
c) Includes only handlers from the target playbook  
d) Dynamically calls a playbook on the control node  
**Answer: b**

---

**5. Which Ansible directive allows dynamic inclusion of handlers based on conditionals?**

a) import_tasks  
b) import_playbook  
c) include_tasks  
d) include_role  
**Answer: c**

---

**6. How do includes and imports affect playbook readability and maintenance?**

a) They make playbooks harder to debug  
b) They simplify complex playbooks into logical chunks  
c) They remove the need for roles  
d) They replace the need for variables  
**Answer: b**

---

**7. What is the impact of using a loop with `import_tasks`?**

a) Each iteration creates a new playbook  
b) It fails, since `import_tasks` is static and not allowed in loops  
c) It runs the same task multiple times  
d) It creates a new tag for each task  
**Answer: b**

---

**8. Which of the following allows a block of tasks to be reused and called conditionally?**

a) import_role  
b) include_tasks  
c) import_tasks  
d) import_playbook  
**Answer: b**

---

**9. Which directive would you use to organize your playbooks into multiple files and combine them at run time?**

a) import_playbook  
b) import_tasks  
c) include_tasks  
d) include_vars  
**Answer: a**

---

**10. What happens if a file included with `import_tasks` does not exist?**

a) The task is skipped with a warning  
b) The playbook will continue execution  
c) The playbook fails during parsing  
d) A backup file is used automatically  
**Answer: c**

---

**11. What happens when you apply a tag to a task file included with `import_tasks`?**

a) Only the included file is tagged  
b) All tasks inside inherit the tag  
c) It disables all tags in that file  
d) Tags must be redefined in the tasks  
**Answer: b**

---

**12. What is a best practice once your playbook is validated and working correctly?**

a) Delete unused roles and collections  
b) Merge it into a single file  
c) Break it up using includes or imports for clarity and reuse  
d) Add all tasks under a single play  
**Answer: c**

