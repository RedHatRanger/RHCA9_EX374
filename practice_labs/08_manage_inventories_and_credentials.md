# Lab: Manage Inventories and Credentials

## Overview

In this lab, you will:

1. Create a **source control credential** to retrieve a playbook from a Git repository.
2. Create a **machine credential** to connect to your managed host.
3. Set up an **inventory** and add the managed host to it.
4. Create a **project** pointing to a Git repo (`controller-review.git`) containing a simple playbook.
5. Define a **job template** that runs `webserver.yml` using a specified execution environment.
6. Confirm the deployed web page by curling or browsing to your host.

### Final Outcome

You’ll see **“Successful playbook run from automation controller.”** when accessing `http://node1.lab.example.com` after the job finishes.

---

## 1. Create a Source Control Credential: **reviewgitcred**

1. Log into Automation Controller at `https://controller.lab.example.com` as **admin/redhat**.
2. Navigate to **Resources → Credentials** → **Add**.
3. Enter:
   - **Name**: `reviewgitcred`
   - **Organization**: `Default`
   - **Credential Type**: `Source Control`
   - **Username**: `rhel`
   - **SCM Private Key**: contents of `/home/rhel/.ssh/gitlab_rsa`
4. **Save**.

---

## 2. Create a Machine Credential: **reviewmachinecred**

1. **Resources → Credentials** → **Add**.
2. Enter:
   - **Name**: `reviewmachinecred`
   - **Organization**: `Default`
   - **Credential Type**: `Machine`
   - **Username**: `devops`
   - **SSH Private Key**: contents of `/home/rhel/.ssh/lab_rsa`
   - **Privilege Escalation Method**: `sudo`
   - **Privilege Escalation Username**: `root`
3. **Save**.

---

## 3. Create an Inventory: **reviewinventory**

1. **Resources → Inventories** → **Add → Add inventory**.
2. Enter:
   - **Name**: `reviewinventory`
   - **Organization**: `Default`
3. **Save**.
4. Click the **Hosts** tab, then **Add**.
5. Enter `node1.lab.example.com` and **Save**.

---

## 4. Create a Project: **reviewproject**

1. **Resources → Projects** → **Add**.
2. Enter:
   - **Name**: `reviewproject`
   - **Organization**: `Default`
   - **Source Control Type**: `Git`
   - **Source Control URL**: `git@git.lab.example.com:rhel/controller-review.git`
   - **Source Control Credential**: `reviewgitcred`
3. **Save**.
4. The project should **sync** automatically. Confirm it **Succeeds**.

---

## 5. Create a Job Template: **reviewtemplate**

1. **Resources → Templates** → **Add → Add job template**.
2. Enter:
   - **Name**: `reviewtemplate`
   - **Inventory**: `reviewinventory`
   - **Project**: `reviewproject`
   - **Execution Environment**: `Automation Hub Default execution environment`
   - **Playbook**: `webserver.yml`
   - **Credentials**: `reviewmachinecred`
3. **Save**.

### 5.1 Launch the Job Template

1. Click **Launch** on `reviewtemplate`.
2. The job runs `webserver.yml`. If it **Succeeds**, you see **Successful** in the job details.

### 5.2 Verify

- In a **web browser** or terminal:
  ```bash
  curl http://node1.lab.example.com
  ```
- Expect this text:
  ```
  Successful playbook run from automation controller.
  ```

---

## 6. Repeatability

- **Delete or rename** the `reviewtemplate`, `reviewinventory`, `reviewmachinecred`, `reviewgitcred`, and `reviewproject` in Automation Controller.
- Recreate them following the steps above.
- If local changes are needed, remove or re-clone the `controller-review.git` repository.

---

## 7. EX374 Exam Objectives Covered

1. **Manage inventories and credentials**: You create a machine credential and add a new inventory.
2. **Manage automation controller**: You create a project, run a playbook from Git, define a job template, and verify the output.
3. **Understand and use Git**: You set up a source control credential and reference a Git repo.

**Lab Complete!**

<br><br><br><br>
## Key Automation Controller Concepts (Summary)
- `Automation Controller` centralizes execution and reporting of Ansible automation.
- A `Project` points to a Git repo (plus optional credentials) to pull Ansible code.
- A `Job Template` ties together inventory, credentials, execution environment, project, and playbook for automated runs.
- Use the `Controller UI` to launch jobs and review their output.
- Validate playbooks locally with `ansible-navigator` in an EE before deploying them in Controller.
