# Nextcloud Ansible Roles

This repository contains Ansible roles for deploying, maintaining, and backing up a Nextcloud Docker instance. The roles included are:

## Deploy Role
Deploys a standard setup of nextcloud with a nextcloud-apache image, redis and mysql. No extra configuration is done.

## Maintenance Role
The maintenance role includes tasks for routine maintenance of the Nextcloud instance, such as updating software and cleaning up logs.

## Backup Role
The backup role includes tasks for backing up Nextcloud data and databases, ensuring data integrity and security.

## Data-migrate Role
Creates a dumb of a localy or nativ running mysql instance and deployes it in a docker mysql container.

## Prerequisites
- [Ansible 2.9+](https://docs.ansible.com/)
- [Docker and Docker Compose](https://docs.docker.com/)
- [Molecule 3.0+](https://ansible.readthedocs.io/projects/molecule/)
- [Python 3.11+](https://realpython.com/installing-python/)

### create local env and install dependences:
Run the following commands before editing code to run the python env and install ansible-molecule:
```bash
python3 -m venv env
source env/bin/activate
pip install -r requirements.txt
```

To install the ansible.community.archive modul.
```bash
ansible-galaxy collection install -r requirements.yml
```


## Repository Structure

<details>
  <summary>Code Structure</summary>

```
.
├── BuildDockerTestImage/
│   ├── Dockerfile
│   └── initctl_faker
├── group_vars/
├── inventories/
│   └── hosts.yml
├── playbooks/
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
│   │   └── tasks/
│   └── migration/
│       ├── tasks/
│       └── vars/
├── ansible.cfg
├── requirements.txt
├── requirements.yml
└── vault.yml
```

</details>

## Configuration

### Ansible Configuration

Ensure the `ansible.cfg` file is present in the root of your project directory with the following settings:
```yml
[defaults]
inventory = ./inventories/hosts.yml
roles_path = ./roles
```

# Inventory
Define your inventory in `inventories/hosts.yml` to specify the target hosts for Ansible playbooks.

# Using the Roles
## Setup
1. **Define Variables**: Ensure all required variables are set in `group_vars/all.yml`.
2. **Templates**: Customize the templates in `roles/{{ roles }}/templates/` as needed.

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

# Testing with [Molecule](https://ansible.readthedocs.io/projects/molecule/)
### Building the Docker Image
Use the Dockerfile in the `roles/deploy` directory to build a testing image.

Build the Docker image:
```bash
docker build -t localhost/debian12-ansible:latest .
```

## Configuring Molecule

In order to create a molecule test setup go into the roles directory you want to test. There you can execute the command:
```bash
molecule init scenario --driver-name docker default
``` 
This command calls the molecule plugin to create a scenario called `default` with `docker` as the driver that shell be used to create the test environment later while executing the `molecule create` command. It could also be `aws`, `kubernetes` or similar container Software tools.

<details>
  <summary>Running Molecule Tests</summary>

Ensure your `molecule/default/molecule.yml` is configured to use the custom Docker image.

## Running Molecule Tests
1. Create instances:
    ```bash
    molecule create | tee molecule_create.log
    ```
2. Run converge:
    ```bash
    molecule converge | tee molecule_converge.log
    ```
3. Verify the Setup:
    ```bash
    molecule verify | tee molecule_verify.log
    ```
4. Destroy instance:
    ```bash
    molecule destroy
    ```
</details>

# Contributing
Feel free to open issues or submit pull requests with improvements or fixes. Contributions are always welcome!

# Good to know

1. Installing molecule is way easier in a python virtual environment.
2. Have docker on your host and remote system installed. It makes life easier.

# License
This project is licensed under the MIT License - see the LICENSE file for details.
