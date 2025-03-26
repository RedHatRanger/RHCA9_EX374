# Lab: Managing Content Collections and Execution Environments

This lab will prepare you for the "Manage content collections" Objectives on the EX374 Exam

Here are the required items:
- **node1.lab.example.com**
- **node2.lab.example.com**
- Username: **rhel**, Password: **redhat**
- A **private automation hub** at `https://hub.lab.example.com`
- The Git repository: `https://github.com/RedHatRanger/manage-review.git`

---

## Lab Scenario

You have **two** Ansible playbooks:
1. **`cockpit.yml`** – Installs/configures Cockpit and modifies firewalld (requires `ansible.posix`).
2. **`create_samples_db.yml`** – Installs MariaDB and creates a `samples` database (requires `community.mysql`).

You will:
1. **Explore** what collections are available in a provided Execution Environment (`ee-supported-rhel8`).
2. **Run** the first playbook (`cockpit.yml`) using that EE.
3. **Discover** the second playbook (`create_samples_db.yml`) needs a collection that is not in that EE.
4. **Install** the missing collection from your private automation hub.
5. **Run** the second playbook successfully.
6. **Verify** the changes on node2.

---

## 1. Obtain the Lab Files

1. On your workstation, create a `~/manage-review/` directory:
   ```bash
   mkdir -p ~/manage-review
   cd ~/manage-review
   ```
2. **Clone** the lab repository:
   ```bash
   git clone https://github.com/RedHatRanger/manage-review.git

   # so you can simply run:
   cd manage-review
   ```
3. You now have the following files:
   - `cockpit.yml`
   - `create_samples_db.yml`
   - `inventory`
   - `ansible.cfg`
   - `requirements.yml` (optional)

**Note**: The `inventory` file references `node1.lab.example.com` and `node2.lab.example.com`, each with username/password `rhel/redhat`.

---

## 2. Review the `cockpit.yml` Playbook

1. From the `manage-review/` directory:
   ```bash
   cat cockpit.yml
   ```
   You should see tasks:
   - Install `cockpit`
   - Enable/start the cockpit service
   - Use the `ansible.posix.firewalld` module to allow cockpit traffic.

The key point is that **`ansible.posix.firewalld`** requires the **`ansible.posix`** collection.

---

## 3. Check Available Execution Environments

We assume you have **ansible-navigator** installed. Run:

```bash
ansible-navigator images
```

You might see something like:

```
Image Tag                Execution environment  Created       Size
0│ee-minimal-rhel8       True                  3 months ago   294 MB
1│ee-supported-rhel8     True                  3 months ago   1.68 GB
```

1. **Select** `1` (or whichever row shows **ee-supported-rhel8**).
2. You’ll see a TUI describing that image:
   - General info
   - Ansible version/collections
   - Python packages
   - OS packages

Press `2` to view **Ansible version and collections**. Look for `ansible.posix` in the listing. Confirm it is present.

Type `:q` to quit.

---

## 4. Run `cockpit.yml` Using `ee-supported-rhel8`

1. Execute:

   ```bash
   ansible-navigator run cockpit.yml --eei ee-supported-rhel8
   ```
   
   **Explanation**:
   - `--eei ee-supported-rhel8` chooses that container.
   - `--pp missing` (playbook-only view) or any preferred option.
   - `-m stdout` outputs to the terminal.

2. You should see tasks succeed on **`node1.lab.example.com`**.

3. **Verify**:
   - Open a browser to `https://node1.lab.example.com:9090`
   - Accept any insecure TLS certificate.
   - Log in with `rhel`/`redhat`.
   - If you see the Cockpit overview screen, the first playbook worked.

---

## 5. Review `create_samples_db.yml`

1. From the `manage-review` directory:
   ```bash
   cat create_samples_db.yml
   ```
   It has tasks:
   - Install `mariadb`, `mariadb-server`.
   - Enable/start `mariadb` service.
   - Use `community.mysql.mysql_db` to create a `samples` database.

2. Notice that `community.mysql.mysql_db` requires the **`community.mysql`** collection.

---

## 6. Inspect the `ee-supported-rhel8` for `community.mysql`

Repeat the earlier step:

```bash
ansible-navigator images
```

- Select `ee-supported-rhel8`.
- Press `2` to see the collections list.
- If `community.mysql` **is not** listed, you cannot run `create_samples_db.yml` in that EE.

Type `:q` to quit.

---

## 7. Install `community.mysql` From Private Automation Hub

Your organization hosts a **private automation hub** at `https://hub.lab.example.com`.

### 7.1 Log In to the Private Automation Hub

1. Open a web browser and go to `https://hub.lab.example.com`.
2. Log in with your provided credentials (for example, `rhel/redhat` or whatever your hub uses).
3. **Navigate** to **Collections → Collections**.
4. Change the filter to the appropriate repository, for example, "Community."  
5. **Search** for `mysql`.
6. You should see `community.mysql` in the results.
7. Click it to see installation details.

### 7.2 Copy the Galaxy Installation Command

Somewhere in the UI, you will see something like:
```
ansible-galaxy collection install community.mysql \
  -s https://hub.lab.example.com/api/galaxy/content/community/ \
  --token YOUR_TOKEN
```

(Your exact command may differ. Use the one displayed in the UI.)

**Alternatively**, if your `ansible.cfg` is configured with the `galaxy_server.community_repo` info (URL + token), you can simply run:

```bash
ansible-galaxy collection install community.mysql
```

### 7.3 Add or Verify `ansible.cfg`

Check your **`ansible.cfg`** that references:
```ini
[galaxy]
server_list = community_repo
[galaxy_server.community_repo]
url=https://hub.lab.example.com/api/galaxy/content/community/
token=YOUR_TOKEN
```

> From the private automation hub web UI, navigate to Collections > API token
> management and then click Load token. Click the Copy to clipboard icon.

### 7.4 Install the Collection Locally

In the `manage-review` directory, you may choose to create a `collections/` folder:

```bash
mkdir -p collections
ansible-galaxy collection install community.mysql -p collections/
```

*(Or do the simpler `ansible-galaxy collection install community.mysql` if your user’s environment is configured to accept the default path.  The key is that we’re pulling from your private automation hub, not galaxy.ansible.com.)*

---

## 8. Run the `create_samples_db.yml` Playbook

Now that `community.mysql` is installed, try:

```bash
ansible-navigator run create_samples_db.yml \
  --eei ee-supported-rhel8 --pp missing -m stdout
```

- The `ee-supported-rhel8` environment might still not have the collection built-in, but if `ansible-navigator` can see a locally installed collection path, it might work (depending on how your environment merges local collections with the container’s `COLLECTIONS_PATHS`).
- Alternatively, you can build a **custom EE** that includes `community.mysql` from your private hub and reference **that** new image instead.

**Result**: The play should succeed, installing MariaDB on **`node2.lab.example.com`** and creating a `samples` DB.

---

## 9. Verify the Database

1. **SSH** to `node2.lab.example.com`:
   ```bash
   ssh rhel@node2.lab.example.com
   # password = redhat
   ```
2. **Check** the DB:
   ```bash
   sudo mysql -e "SHOW DATABASES"
   ```
   If prompted for sudo password, enter `redhat`.
3. You should see:
   ```
   +--------------------+
   | Database           |
   +--------------------+
   | information_schema |
   | mysql             |
   | performance_schema|
   | samples           |
   +--------------------+
   ```
4. The `samples` database means success.

---

## (Optional) Steps in Automation Controller

If you’d prefer to replicate these steps **within** Automation Controller:

1. Create a **Project** pointing to the `https://github.com/RedHatRanger/manage-review.git` repo.
2. Create an **Inventory** with hosts `node1.lab.example.com` and `node2.lab.example.com`.
3. Create a **Machine Credential** for `rhel/redhat`.
4. Create a **Job Template** using `cockpit.yml` with `node1`.
5. Launch the job using an EE that has `ansible.posix`.
6. Create another **Job Template** with `create_samples_db.yml` referencing `node2`.
7. If `community.mysql` is missing, either install it or build a custom EE.

These steps mirror the CLI approach but inside the Controller UI.

---

## 10. Conclusion

In this lab, you:

- Cloned the **manage-review** repo containing `cockpit.yml` and `create_samples_db.yml`.
- Confirmed that the first playbook uses **`ansible.posix`** (available in the `ee-supported-rhel8` environment).
- Found that the second playbook needs **`community.mysql`** (not present by default), so you installed or added it from a **private automation hub**.
- Verified successful deployment of Cockpit on node1 and a `samples` DB on node2.

<br><br><br><br>
## Key Tips for Working with Collections and Execution Environments
- Use `ansible-navigator doc <module> --mode stdout` to view module documentation from available collections.
- Run `ansible-navigator images` to inspect available execution environments and their included collections.
- Always use `Fully Qualified Collection Names (FQCNs)` when referencing the `<namespace>.<module>`, roles, and plugins.
- Install collections locally with `ansible-galaxy collection install`, using -p <path> to specify the install location (unless your ansible.cfg sets `collections_path`)
- List required collections in a `collections/requirements.yml` file for your project.
- Execution environments can use collections stored in the project's local `collections/` directory.
- `ee-supported-rhel8` is the default EE and includes `Red Hat Certified` and `ansible.builtin` collections.
- `ee-minimal-rhel8` includes only `ansible.builtin`, but supports using local collections.
- Use `ee-29-rhel8` if your playbooks require Ansible 2.9 compatibility.
