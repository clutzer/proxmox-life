# Proxmox-Life 🚀

A suite of automation scripts for managing a Software-Defined HomeLab on Proxmox VE. This repository standardizes the creation, deployment, and destruction of Ubuntu-based VMs using Cloud-Init and Python orchestration.

## 🏗️ Architecture
This workflow uses the **Golden Image** philosophy. Instead of manual ISO installations, we use official Ubuntu Cloud Images, specialized via Cloud-Init snippets for "Operational Excellence."

* **Base Template**: Ubuntu 24.04 (Noble Numbat)
* **Provisioning**: Cloud-Init (User data snippets)
* **Storage**: Optimized for NAS-backed disk images (NFS/SMB)
* **Communication**: QEMU Guest Agent enabled by default

## 🛠️ The Toolkit

| Script | Language | Description |
| :--- | :--- | :--- |
| `create-vm-template` | Bash | Downloads the Ubuntu Cloud Image, sets VirtIO hardware, and creates Template 9000. |
| `create-vm` | Python | Clones the template, specializes RAM/Cores/Disk, and injects Cloud-Init identity. |
| `destroy-vm` | Python | Safely stops and purges VMs. Includes an interactive picker and a protected-list check. |

## 📋 Setup & Prerequisites

### 1. Identity Snippet
Create a Cloud-Init snippet on your Proxmox host to define your default user and SSH keys.
**Path:** `/var/lib/vz/snippets/sa-user.yaml`

```yaml
#cloud-config
users:
  - name: sa
    groups: sudo
    shell: /bin/bash
    sudo: 'ALL=(ALL) NOPASSWD:ALL'
    ssh_authorized_keys:
      - ssh-ed25519 AAAAC3... # Your Public Keys
packages:
  - qemu-guest-agent
runcmd:
  - systemctl enable --now qemu-guest-agent
