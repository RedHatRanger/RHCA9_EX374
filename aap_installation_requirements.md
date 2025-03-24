# Ansible Automation Platform 2.4 Installation Requirements

This document outlines the requirements for setting up a single-node Ansible Automation Platform 2.4 installation with Automation Controller, Private Automation Hub, and PostgreSQL database on Proxmox.

## System Requirements

### Base System Requirements

| Requirement | Specification |
|-------------|---------------|
| **Subscription** | Valid Red Hat Ansible Automation Platform |
| **OS** | Red Hat Enterprise Linux 8.8 or later 64-bit, or Red Hat Enterprise Linux 9.0 or later 64-bit |
| **Ansible-core** | Version 2.14 or later |
| **Python** | 3.9 or later |
| **Browser** | Current version of Mozilla Firefox or Google Chrome |
| **Database** | PostgreSQL version 13 |

### Hardware Requirements for Single-Node Setup

For a single-node setup combining Automation Controller and Private Automation Hub:

| Component | RAM | CPUs | Disk Space |
|-----------|-----|------|------------|
| Automation Controller | 16 GB minimum | 4 minimum | 40 GB minimum with at least 20 GB available under /var/lib/awx |
| Private Automation Hub | 8 GB minimum | 2 minimum | 60 GB minimum |
| Combined Setup | 24 GB recommended | 6 recommended | 100 GB recommended |

**Note:** While it's possible to install both components on a single node, Red Hat recommends separate nodes for production environments to avoid resource contention.

## Proxmox VM Configuration

For a lab environment on Proxmox, the following VM configuration is recommended:

- **vCPUs**: 6 (minimum)
- **RAM**: 24 GB (minimum)
- **Storage**: 100 GB (minimum)
- **Network**: Bridge mode with internet access
- **OS**: RHEL 8.8 or RHEL 9.0+

## Installation Bundle

The AAP setup bundle is a self-contained package that includes all necessary components for installation, including:

- Automation Controller
- Private Automation Hub
- PostgreSQL database
- Required dependencies

The bundle can be downloaded from the Red Hat Customer Portal and is the recommended method for installing AAP in environments without direct internet access.

## Inventory File Configuration

For a single-node setup with Automation Controller and Private Automation Hub using an internal PostgreSQL database, the following inventory file structure can be used:

```ini
[automationcontroller]
aap.example.com

[automationhub]
aap.example.com

[database]
aap.example.com

[all:vars]
admin_password='redhat'
pg_host='aap.example.com'
pg_port='5432'
pg_database='awx'
pg_username='awx'
pg_password='redhat'
pg_sslmode='prefer'

registry_url='registry.redhat.io'
registry_username='<registry username>'
registry_password='<registry password>'

automationhub_admin_password='redhat'

automationhub_pg_host='aap.example.com'
automationhub_pg_port=5432
automationhub_pg_database='automationhub'
automationhub_pg_username='automationhub'
automationhub_pg_password='redhat'
automationhub_pg_sslmode='prefer'
```

**Note:** Replace `aap.example.com` with your actual hostname or IP address. All passwords are set to 'redhat' as requested.

## Prerequisites

Before installation:

1. Ensure root access is available (via sudo or direct)
2. Configure NTP client on all nodes
3. Ensure required network ports are open
4. Register the system with Red Hat Subscription Manager
5. Enable required repositories

## Installation Process Overview

1. Download the AAP setup bundle
2. Extract the bundle
3. Configure the inventory file
4. Run the setup script
5. Verify the installation

Detailed installation steps will be provided in the AAP installation guide.
