# RHCA Ansible Automation Platform (AAP) Study Guide

Welcome to the RHCA Ansible Automation Platform Study Guide! This comprehensive guide is designed to help you prepare for the Red Hat Certified Specialist in Developing Automation with Ansible Automation Platform (EX374) exam while setting up a complete single-node AAP environment on Proxmox.

## Overview

This study guide provides:

1. **Installation Guides**: Step-by-step instructions for setting up a single-node AAP environment on Proxmox
2. **Guided Labs**: Comprehensive labs covering all EX374 exam objectives
3. **Sample Code**: Ready-to-use playbooks, roles, and configurations
4. **Best Practices**: Recommendations for working with Ansible Automation Platform

## Environment Setup

This guide assumes you have a server with Proxmox 8.13+ already installed. The recommended hardware specifications are:

- CPU: 4+ cores (8+ recommended)
- RAM: 16GB+ (32GB+ recommended)
- Storage: 100GB+ SSD/NVMe storage
- Network: 1Gbps+ network interface

### Setup Guides

1. [Proxmox VM Setup Guide](guides/proxmox_setup_guide.md)
2. [AAP Installation Guide](guides/aap_installation_guide.md)
3. [PostgreSQL Configuration Guide](guides/postgres_configuration_guide.md)

## EX374 Exam Objectives and Labs

The following labs cover all the objectives for the EX374 exam:

1. [Git Repository Management](labs/01_git_repository_management.md)
2. [Ansible Playbook Development](labs/02_ansible_playbook_development.md)
3. [Task Execution Management](labs/03_task_execution_management.md)
4. [Data Transformation](labs/04_data_transformation.md)
5. [Task Delegation](labs/05_task_delegation.md)
6. [Content Collection Management](labs/06_content_collections.md)
7. [Execution Environment Management](labs/07_execution_environments.md)
8. [Inventory and Credential Management](labs/08_inventory_credential_management.md)
9. [Automation Controller Management](labs/09_automation_controller_management.md)

## Single-Node AAP Setup

This guide focuses on setting up a single-node AAP environment that includes:

1. **Automation Controller**: The web UI and API for Ansible automation
2. **Private Automation Hub**: A repository for Ansible content
3. **PostgreSQL Database**: The database backend for AAP components

All components are installed on a single Proxmox VM for simplicity and resource efficiency.

## Credentials

Throughout this guide, we use the following standard credentials:

- **Ansible User**: `rhel`
- **Ansible Password**: `redhat`
- **Database User**: `postgres`
- **Database Password**: `redhat`
- **AAP Admin User**: `admin`
- **AAP Admin Password**: `redhat`

## Getting Started

1. Begin by setting up your Proxmox VM using the [Proxmox VM Setup Guide](guides/proxmox_setup_guide.md)
2. Install AAP using the [AAP Installation Guide](guides/aap_installation_guide.md)
3. Configure PostgreSQL using the [PostgreSQL Configuration Guide](guides/postgres_configuration_guide.md)
4. Work through the labs in order, starting with [Git Repository Management](labs/01_git_repository_management.md)

## Additional Resources

- [Red Hat Ansible Automation Platform Documentation](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/)
- [Ansible Documentation](https://docs.ansible.com/)
- [EX374 Exam Information](https://www.redhat.com/en/services/training/red-hat-certified-specialist-developing-automation-ansible-automation-platform-exam)

## Troubleshooting

If you encounter issues during setup or while working through the labs, check the following:

- Ensure your hardware meets the minimum requirements
- Verify network connectivity between components
- Check system logs for error messages
- Ensure all services are running properly
- Verify that all credentials are correct

## Contributing

This study guide is a work in progress. If you find errors or have suggestions for improvements, please submit an issue or pull request.

## License

This project is licensed under the GPL-3.0 License - see the LICENSE file for details.
