[![Build](https://img.shields.io/github/actions/workflow/status/j2eff-we/lab-infra/ci.yml?branch=main)](https://github.com/j2eff-we/lab-infra/actions)
[![License](https://img.shields.io/github/license/j2eff-we/lab-infra)](https://github.com/j2eff-we/lab-infra/blob/main/LICENSE)
[![Last Commit](https://img.shields.io/github/last-commit/j2eff-we/lab-infra)](https://github.com/j2eff-we/lab-infra/commits/main)
[![Built with Packer](https://img.shields.io/badge/built%20with-Packer-blue)](https://www.packer.io/)
[![Infra: Terraform](https://img.shields.io/badge/infra-Terraform-5f43e9)](https://www.terraform.io/)

# Local Infrastructure Lab with Packer & Terraform (libvirt)

This repository provides a reusable lab automation framework to build and manage local virtual machines using **Packer** and **Terraform (libvirt)**.

It is designed to serve as the foundation for practicing and testing infrastructure platforms like **MinIO**, **Ceph**, and **Kubernetes** on your local machine.

---

## Project Structure

```
.
├── base/packer/             # Packer configuration for Ubuntu 22.04
│   ├── jammy.json           # Packer build definition for 
│   ├── ubuntu.pkr.hcl       # (Optional) HCL format config for init
│   ├── http/                # cloud-init files served during build
│   │   ├── meta-data
│   │   └── user-data
│   └── scripts/             # Provisioning scripts used during image build
│       ├── clean.sh
│       └── minimize.sh
│
├── base/terraform/          # Terraform config to provision VMs via libvirt
│   └── main.tf
│ 
│ 
├──labs/                     # Each Lab environments 
│   └── minio/
│   └── k8s
│   └── ceph/
│   
├── Makefile                 # Automation for setup and installation
└── README.md
```
### Detailed Breakdown

- `base/packer/jammy.json`  
  Defines how to build a custom **Ubuntu 22.04 (Jammy Jellyfish)** image using Packer. Includes boot setup, provision scripts, and output settings (e.g., `qcow2` format).

- `base/packer/http/`  
  Contains `user-data` and `meta-data` files used by **cloud-init** during image provisioning. These are served by Packer via `http_directory` to initialize system settings inside the VM.

- `base/packer/scripts/`  
  Shell scripts executed during image build (e.g., install packages, enable SSH, clean up cache). These customize the base image for downstream lab usage.

- `base/terraform/main.tf`  
  Terraform configuration to create local virtual machines using **libvirt**. Useful for spinning up VMs based on the image built with Packer.

- `labs/minio/`  
    Contains configuration files (e.g., Ansible playbooks, inventory, Terraform) for setting up a MinIO lab environment on local VMs.
    This includes automation scripts to deploy a multi-disk, S3-compatible MinIO cluster using the image built via Packer.
    This structure is extensible — additional labs such as ceph/, k8s/, or vault/ can be added in the same way.

- `Makefile`  
  Provides convenient setup targets:
  - `make prepare` — Installs dependencies, `tfenv`, and `packer`
  - `make configure-libvirt` — Configures `libvirt` and group permissions
  - `make setup-sudoers` — Grants passwordless `sudo` for the current user

---

## Blog Series Scope (In Progress)

This repository is currently being initialized and will support a blog series covering:

- **Packer**: Building custom Ubuntu 22.04 VM images using `cloud-init`
- **Terraform (libvirt)**: Creating and managing local VMs using the `libvirt` provider

> This repo will later be extended with MinIO, Ceph, and Kubernetes lab configurations.
---

## Features

- Automated build of Ubuntu QCOW2 images using Packer (cloud-init enabled)
- Local libvirt environment setup and configuration
- VM provisioning with Terraform (libvirt provider)
- Ready-to-use for MinIO, Ceph, Kubernetes, and more

---

## Getting Started

### 1. Install System Dependencies

```bash
sudo apt update && sudo apt install -y build-essential curl git sudo
```

### 2. Enable Passwordless Sudo for Your User

```bash
make setup-sudoers
exec su -l $(whoami)  # Re-login to apply sudoers change
```

### 3. Prepare Your Environment
```bash
make prepare
exec su -l $(whoami)  # Re-login to apply group memberships (libvirt, kvm)
```

###  4. Build the Base Ubuntu Image
```bash
cd base/packer
packer init ubuntu.pkr.hcl
packer build jammy.json
```

⸻
## Use Cases

This lab environment is ideal for:

- **MinIO Lab** — Deploy a multi-disk, S3-compatible object storage service for testing.
- **Ceph Lab** — Experiment with MON/OSD setup and validate RBD or CephFS configurations.
- **Kubernetes Lab** — Build a local Kubernetes cluster using `kubeadm` for development and experimentation.

The base image built via Packer can be reused across these labs, enabling fast and consistent prototyping.

---

## Philosophy

This repository is built on the following principles:

- Provide a **consistent and minimal base image** for infrastructure labs.
- Enable **repeatable, scriptable workflows** through Makefiles and automation.
- Support **rapid local iteration** on cluster setups without relying on cloud resources.

---

## Contributions

We welcome contributions and ideas!

Feel free to:

- Open issues or submit pull requests  
- Extend the lab scenarios (e.g., add new roles or services)  
- Customize the Makefile tasks to fit your own workflows

## License

This project is licensed under the MIT License.  
See [LICENSE](https://github.com/j2eff-we/lab-infra/blob/main/LICENSE) for details.