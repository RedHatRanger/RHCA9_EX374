**Lab 9: Managing Automation Controller**

**Objectives**

1. **Run playbooks** in Automation Controller using job templates.
2. **Pull automation content** into Automation Controller from Git repositories or Automation Hub.
3. **Pull an execution environment** from Automation Hub and configure Automation Controller to run playbooks within it.

---

### Introduction

In this lab, you’ll leverage Automation Controller (Ansible Automation Platform 2.4) to orchestrate automation workflows. You will set up projects from both Git and Automation Hub, use predefined execution environments from Automation Hub, and configure job templates to automate playbook execution.

### Environment & Setup

- **Automation Controller**: `controller.lab.example.com` (user `rhel`, password `redhat`)
- **Automation Hub**: `hub.lab.example.com` (user `student`, password `redhat123`)
- **Git repository**: Your GitHub repository (used previously)

Log in to the Automation Controller UI:

```
https://controller.lab.example.com
```

---

## 1. Pull Content into Controller (Git & Automation Hub) ← Objective 2

### A. Pull from Git

1. In Automation Controller, navigate to **Projects → Add**.
2. Enter details:
   - **Name:** `lab9-git-project`
   - **SCM Type:** Git
   - **SCM URL:** (your GitHub repo URL)
   - **Credential:** Choose your previously configured source control credential.
3. Save and verify synchronization.

### B. Pull from Automation Hub

1. Navigate to **Projects → Add**.
2. Enter details:
   - **Name:** `lab9-hub-project`
   - **SCM Type:** Automation Hub
   - **Content Source:** Your previously published collection
3. Save and verify synchronization.

---

## 2. Pull Execution Environment from Automation Hub ← Objective 3

1. In Automation Controller, go to **Execution Environments → Add**.
2. Enter details:
   - **Name:** `lab9-custom-ee`
   - **Image URL:** `hub.lab.example.com/custom-ee:latest`
3. Save and confirm the image is pulled successfully.

---

## 3. Run Playbooks Using Job Templates ← Objective 1

1. Navigate to **Templates → Add Job Template**.
2. Configure your job template:
   - **Name:** `lab9-job-template`
   - **Inventory:** Select an existing inventory.
   - **Project:** Select either `lab9-git-project` or `lab9-hub-project`.
   - **Execution Environment:** Select `lab9-custom-ee`.
   - **Playbook:** Select a playbook from your project.
   - **Credential:** Select your previously created machine credential.
3. Launch the job template and monitor the job execution details.

---

### 4. Verify & Wrap-up

- Verify your playbook runs successfully using the configured Execution Environment and Automation Controller.
- Ensure synchronization with Automation Hub and Git repositories.
- Confirm that job templates execute correctly.

**Lab Outcome:**

You successfully integrated Automation Controller with external content sources, execution environments from Automation Hub, and managed playbook execution centrally.

*End of Lab 9: Managing Automation Controller*
