# Ansible Practical Project

**One Controller. Two Linux Servers. One Playbook.**

## About the Project

This project was built as part of my Ansible course.

The goal is to automate the setup and management of two different Linux servers using a single Ansible playbook.

Instead of configuring each server manually, Ansible connects over SSH and handles the configuration automatically.

---

## My Lab Environment

The project runs on three VMware virtual machines:

- **MASTER** - Ansible Controller
- **Ubuntu 22.04.5 LTS** - Managed Server
- **Fedora 44** - Managed Server

The controller communicates with both servers using SSH keys.

## What Does the Playbook Do?

The automation is divided into three Ansible Roles.

### 1. Common Role

Handles basic server configuration:

- Creates a user named `student`
- Configures SSH access and sudo permissions
- Removes sudo permissions from the original user
- Updates Linux packages
- Reboots servers only when necessary

### 2. Docker Role

Prepares both Linux servers to run containers:

- Installs Docker from the official repositories
- Enables and starts Docker
- Adds `student` to the Docker group
- Configures Docker log rotation
- Authenticates with Docker Hub using a read-only token

### 3. App Role

Deploys and verifies the application:

- Runs an `nginx:alpine` container named `exam-app`
- Loads environment variables from a local file
- Exposes nginx through port `8080`
- Checks the container and HTTP response
- Collects logs from both servers

---

## Running the Project

All commands are executed from the Ansible Controller.

**Step 1 - Install the required collections**

`ansible-galaxy collection install -r requirements.yml`

**Step 2 - Configure the environment**

Update the inventory with the target server IP addresses and SSH settings.

Make sure the encrypted Vault file is configured and the Vault password is available separately.

**Step 3 - Run the playbook**

`ansible-playbook site.yml --ask-vault-pass`

For initial setup when the SSH user requires a sudo password, add `--ask-become-pass`.

---

## Application Details

- **Image:** `nginx:alpine`
- **Container:** `exam-app`
- **Host Port:** `8080`
- **Container Port:** `80`
- **Restart Policy:** `unless-stopped`

The environment variables are defined in `files/app.env` on the controller.

The file itself is not copied to the managed servers.

## Security

Sensitive information is stored in an encrypted Ansible Vault file.

This includes the student password hashes and Docker Hub credentials.

The Docker Hub token has read-only permissions, and sensitive authentication tasks use `no_log: true`.

The Vault password is provided separately and is never committed to GitHub.

## Testing and Results

I verified the project using Ansible and Docker commands.

The following checks passed:

- SSH connectivity to both servers
- Docker installation and service status
- Container status and environment variables
- HTTP response `200 OK` from both nginx containers
- Ansible Lint with zero failures
- Idempotency with `changed=0` on both servers

Container logs and HTTP results are saved in the `logs/` directory.

Playbook execution evidence is stored in the `evidence/` directory.
