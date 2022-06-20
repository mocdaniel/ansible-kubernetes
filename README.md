# ansible-kubernetes

This Ansible role prepares hosts for running Kubernetes clusters on them:

- [x] Disables swap if necessary
- [X] Adds kernel modules and changes network settings
- [x] Disables SELinux if necessary
- [x] Installs [containerd](https://containerd.io)
- [x] Installs **kubeadm, kubectl**, and **kubelet**

It currently works for the following distributions:

- [x] [RockyLinux8](https://rockylinux.org)

## Usage:

### Setup

You will need to install `ansible` in order to execute the playbooks. This is best to be done within a **virtual environment**:

```
git clone https://github.com/mocdaniel/ansible-kubernetes && cd ansible-kubernetes
python3 -m venv venv && source venv/bin/activate
pip install ansible
```

You will also need to copy an SSH key for the standard user to the remote host(s):

```
ssh-keygen -f ~/.ssh/ansible-key # create a key if needed
ssh-copy-id -i ~/.ssh/ansible-key rocky@raspberry-ip
```

### Playbook and inventory

An example playbook could look like this:

```yaml
---
- hosts: raspberries
  gather_facts: yes
  become: yes
  vars:
    kubernetes_version: 1.22.3-0
  roles:
  - role: ansible-kubernetes
```

and could get its host information from an inventory file like this:

```ini
[raspberries]
raspberry-master ansible_host=192.168.178.188 ansible_user=rocky ansible_private_key_file=~/.ssh/ansible-key
raspberry-worker-1 ansible_host=192.168.178.198 ansible_user=rocky ansible_private_key_file=~/.ssh/ansible-key
```

Note how the key you created and copied to the remote Raspberry is being used by Ansible here.

### Configurable options

Some options should be given at runtime due to security reasons:

| variable         | value        | required | notes                                                |
|:----------------:|:------------:|:--------:|:----------------------------------------------------:|
| ansible_sudo_pass| `rockylinux` | **yes**  | as alternative, grant `rocky` passwordless root      |


Thus, a valid execution of this role could look something like this:

```
ansible-playbook book.yml -i hosts.ini --extra-vars="ansible_sudo_pass=rockylinux"
```

## Contributing

Open **issues** or **feature requests** using the respective issue templates or add to the repository yourself and create a **pull request**!

### Development setup

Much like the setup for plain usage of this repository, setting up your development environment works best using a **virtual environment**:

```
git clone https://github.com/mocdaniel/ansible-kubernetes && cd ansible-kubernetes
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
```

Now, all required packages for testing and debugging the Ansible role while developing should be installed.

### Testing

In order to avoid more work *after* creating a pull request, you can test your changes locally, but you will need to install [Docker](https://docs.docker.com/get-docker/) to do so:

```
# make sure Docker is running and you activated the virtual environment
molecule test .
```

You will see a lot of output on your CLI, which hopefully will be mainly **green/yellow**.

Don't worry about creating pull requests, though! Your contribution will be tested automatically once you open one. 

## License

This project is licensed under **MIT license**. You can do *basically* whatever you want with it. For more information and a copy of the license, please see [LICENSE](LICENSE).
