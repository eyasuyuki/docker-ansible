Provisioning Docker by Ansible
==============================

## Requirements

- OS X 10.11.5
- Ansible 2.1
- Docker 1.11.2
- Docker Machine 0.7.0
- Docker Compose 1.7.1
- VirtualBox 5.1.0

## Install Ansible, Docker, VirtualBox

```
brew install ansible docker docker-machine docker-compose
brew cask install virtualbox
```

## Create Linux VM by Docker Machine

```
docker-machine create -d virtualbox <vm name>
```

For example, VM name is docker01,

```
docker-machine create -d virtualbox docker01
```

## Setup Docker environment variables

```
eval $(docker-machine env <vm name>)
```

For example, VM name is docker01,

```
eval $(docker-machine env docker01)
```

## Start Container by Docker Compose

install.yml
```
centos:
  image: centos:latest
  container_name: centos7
  entrypoint: tail -f /dev/null
```

```
docker-compose -f install.yml up -d
```

### Check Containers by Docker Compose

```
docker-compose -f install.yml ps
```

## Ansible settings

ansible.cfg
```
[defaults]
inventory = ./hosts
```

hosts
```
[docker]

centos7
```

## Test Ansible Connection

```
ansible -i hosts centos7 -m ping -c docker
```

If success,

```
centos7 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
```

## Ansible Playbook example

setup.yml
```
- hosts: all
  connection: docker
  roles:
    - common
```

## Provision by Ansible

```
ansible-playbook setup.yml -vvvv
```

## Docker commit

If you want to save docker image, Put that line into your Ansible Playbook.

```
- name: commit changes
  local_action: shell /usr/local/bin/docker commit {{ inventory_hostname }} <image name>
```

## Run bash on Docker container

```
docker exec -it centos7 bash
```

