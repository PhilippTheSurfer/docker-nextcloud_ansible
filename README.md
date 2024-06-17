# Nextcloud Ansible Roles

This repository contains Ansible roles for deploying, maintaining, and backing up a Nextcloud Docker instance. The roles included are:

- **deploy**: Sets up and deploys Nextcloud using Docker and Docker Compose.
- **maintenance**: Contains tasks for routine maintenance of the Nextcloud instance.
- **backup**: Handles backing up the Nextcloud data and database.

## Prerequisites
- Ansible 2.9+
- Docker and Docker Compose
- Molecule 3.0+
- Python 3.11+

Run the following commands before editing code:
```bash
python3 -m venv env
```
To create a python virtual environment.
```bash
source env/bin/activate
```
Only for Linux. If you are running windows `./env/lib/activate` will do the trick.
```bash
pip install -r requirements.txt
```
To install molecule and python dependence. 
```bash
ansible-galaxy collection install -r requirements.yml
```
To install the ansible archive module to run the backup playbook.

## Repository Structure

```
.
├── ansible.cfg
├── inventories/
│   └── hosts.yml
├── roles/
│   ├── deploy/
│   │   ├── tasks/
│   │   ├── handlers/
│   │   ├── molecule/
│   │   │   └── default/
│   │   │       ├── converge.yml
│   │   │       ├── molecule.yml
│   │   │       └── prepare.yml
│   │   └── templates/
│   ├── maintenance/
│   │   ├── tasks/
│   │   ├── handlers/
│   │   ├── molecule/
│   │   │   └── default/
│   │   │       ├── converge.yml
│   │   │       ├── molecule.yml
│   │   │       └── prepare.yml
│   │   └── templates/
│   ├── backup/
│       └── tasks/
└── playbook.yml

```

## Configuration

### Ansible Configuration

Ensure the `ansible.cfg` file is present in the root of your project directory with the following settings:
```yml
[defaults]
inventory = ./inventories/hosts.yml
roles_path = ./roles
remote_tmp = /tmp/.ansible/tmp
retry_files_enabled = False
```

# Inventory
Define your inventory in `inventories/hosts.yml` to specify the target hosts for Ansible playbooks.

# Using the Deploy Role
## Setup
1. **Define Variables**: Ensure all required variables are set in `roles/deploy/vars/main.yml`.
2. **Templates**: Customize the templates in `roles/deploy/templates/` as needed.

# Running the Playbook

To deploy Nextcloud, run the following command:
```bash
ansible-playbook playbooks/deploy.yml --ask-vault-pass
```

### Ansible Vault

I recommand using *ansible vault*. Atleast the playbook already includes one. Create it in the repositorys root directory with the command:
```bash
ansible-vault create vault.yml
```
Now you can store your server passwords secure.

**Example `vault.yml`:**
```yml
vault_ansible_password: "somesecurepassword"
```

### Example `inventories/host.yml`
I like to use the yml way of managing my hosts. Below is a simple example how to setup a inventory. Be care full what you name it though since the default name is set in the `ansible.cfg` to be `inventories/hosts.yml`. 

**Example `inventories/host.yml`:** 
```yml
all:
  hosts:
    127.1.1.1:
      ansible_user: username # provide the username
      # ansible_ssh_private_key_file: ~/.ssh/serversshkey # in case you use ssh key (you should)
      ansible_become_pass: "{{ vault_ansible_password }}" #variable defiened in vault.yml that points to the password of the user
      ansible_become_method: sudo
      ansible_port: 22
```

# Testing with Molecule
### Building the Docker Image
Use the Dockerfile in the `roles/deploy` directory to build a testing image.

Build the Docker image:
```bash
docker build -t localhost/debian12:test .
```

## Configuring Molecule
Ensure your `molecule/default/molecule.yml` is configured to use the custom Docker image.

## Running Molecule Tests
1. Create instances:
    ```bash
    molecule create
    ```
2. Run converge:
    ```bash
    molecule converge | tee molecule_converge.log
    ```
3. Verify the Setup:
    ```bash
    molecule verify
    ```
4. Destroy instance:
    ```bash
    molecule destroy
    ```

# Roles
## Deploy Role
Deploys a standard setup of nextcloud with a nextcloud-apache image, redis and mysql. No extra configuration is done.

## Maintenance Role
The maintenance role includes tasks for routine maintenance of the Nextcloud instance, such as updating software and cleaning up logs.

## Backup Role
The backup role includes tasks for backing up Nextcloud data and databases, ensuring data integrity and security.

# Contributing
Feel free to open issues or submit pull requests with improvements or fixes. Contributions are always welcome!

# Good to know

1. Always use a python virtual environment if you write code that needs to be tested.
2. Installing molecule is way easier in a python virtual environment.
3. Have docker on your host and remote system installed. It makes life easier.

# License
This project is licensed under the MIT License - see the LICENSE file for details.
