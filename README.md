# Ansible Projects

A collection of hands-on Ansible projects for automating server configuration.

## Projects

### 1. Install Packages on Two EC2 Instances

**Folder:** [install_packages_on_two_ec2](install_packages_on_two_ec2/)

Installs a common set of packages on two Ubuntu EC2 worker nodes from a single Ansible control node, then verifies each package is installed.

**Packages installed:** `nginx`, `tree`, `zip`, `unzip`, `docker.io`

**Files:**

| File | Purpose |
| ---- | ------- |
| `ansible.cfg` | Points to the inventory, disables host key checking, sets the Python interpreter and SSH private key |
| `host.ini` | Inventory with the `servers` group (`worker-1`, `worker-2`) |
| `playbook.yml` | Installs the packages with `apt`, checks them with `dpkg -l`, and prints the results |

**Prerequisites:**

- An Ansible control node (Ubuntu) with Ansible installed
- Two Ubuntu EC2 instances reachable over SSH as the `ubuntu` user
- The SSH private key at `/home/ubuntu/.ssh/id_rsa` on the control node
- Security groups allowing SSH (port 22) from the control node

**Usage:**

```bash
cd install_packages_on_two_ec2

# Update host.ini with your EC2 public IPs, then check connectivity
ansible servers -m ping

# Run the playbook
ansible-playbook playbook.yml
```

> Note: EC2 public IPs change when instances are stopped and started, so update `host.ini` before each run.

### 2. Handling Secrets with Ansible Vault

**Folder:** [Handling_secrets](Handling_secrets/)

Loads secrets (name, password, API key) from a vars file into a playbook and shows how to keep sensitive values out of the output with `no_log`.

**Files:**

| File | Purpose |
| ---- | ------- |
| `ansible.cfg` | Points to the inventory, disables host key checking, sets the Python interpreter and SSH private key |
| `host.ini` | Inventory with the `servers` group (`worker-1`, `worker-2`) |
| `secrets.yaml` | Variables file holding the secrets (`name`, `password`, `apiKey`) |
| `show_secrets.yml` | Loads `secrets.yaml` via `vars_files`, prints the name, and hides the password and API key with `no_log: true` |
| `vault.txt` | Vault password file used to encrypt and decrypt `secrets.yaml` |

**Usage:**

```bash
cd Handling_secrets

# Encrypt the secrets file with the vault password
ansible-vault encrypt secrets.yaml --vault-password-file vault.txt

# View or edit the encrypted file
ansible-vault view secrets.yaml --vault-password-file vault.txt
ansible-vault edit secrets.yaml --vault-password-file vault.txt

# Run the playbook (Ansible decrypts secrets.yaml at runtime)
ansible-playbook show_secrets.yml --vault-password-file vault.txt
```

> Note: The values in this folder are demo values for learning. In a real project, never commit the vault password file. Add it to `.gitignore` and keep it outside the repo.
