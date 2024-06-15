# Nextcloud Ansible Roles

This repository contains Ansible roles for deploying, maintaining, and backing up a Nextcloud Docker instance. The roles included are:

- **deploy**: Sets up and deploys Nextcloud using Docker and Docker Compose.
- **maintenance**: Contains tasks for routine maintenance of the Nextcloud instance.
- **backup**: Handles backing up the Nextcloud data and database.

## Prerequisites
Ansible 2.9+
Docker and Docker Compose
Molecule 3.0+
Python 3.11+

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
│   │   ├── templates/
│   │   └── vars/
│   ├── maintenance/
│   │   └── tasks/
│   ├── backup/
│   │   └── tasks/
├── molecule/
│   └── default/
│       ├── converge.yml
│       ├── molecule.yml
│       └── prepare.yml
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

I recommand using *ansible vault*. Atleast the playbook already includes one. Create it in the repositorys root directory with the command:
```bash
ansible-vault create vault.yml
```
Now you can store your server passwords secure.

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

# Other Roles
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
