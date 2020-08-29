# Ghost Blog deployed in docker using ansible and vagrant

## Prerequisites

- Vagrant
- Virtualbox

## Getting started

For starting all you need is vagrant and virtualbox, once you have them you can start by installing the
virtualbox guest additions plugin by vagrant and getting the VM up.

Run the following command for installing the plugin:

```sh
vagrant plugin install vagrant-vbguest
```

This is required because the virtualbox guest additions in the VM might be a different version from the host version of VirtualBox
and this causes problems while sharing folders.
Once the plugin is installed, you can start the VM:

```sh
vagrant up
```

This might take a while since it will be downloading the debian box, then it will install all of the required tools
and docker images to run ghost inside docker in the VM.

## Project layout

The project uses vagrant to spin up a debian 10 VM and then uses the `shell` and `ansible_local` vagrant provisioners.
The `shell` is used to execute the [bootstrap.sh](./bootstrap.sh) which will install ansible in the VM, we do this so we can use later the `ansible_local` provisioner to install and configure docker and start our docker compose project.

1. [Vagrantfile](./Vagrantfile) (This should be self explanotory, we configure a VM with 2 GB of RAM, share this folder to the VM at `/vagrant_data`, forward the port from the host to the guest `443 -> 443`, run the `shell` and `ansible_local` provisioners)
2. [playbook](playbook.yml) (This is the main ansible playbook which will be run inside of the VM, it uses our custom roles to configure the VM)
3. [ansible.cfg](./ansible.cfg) (This is the configuration file which will be used by ansible, the only thing that we define here is which python interpreter ansible should use, this is required since some of the tasks in the roles use `pip` to install modules)
4. [roles](./roles) (Here we define our custom roles, `debian-base` for updating the system and installing some required packages, `debian-docker` for installing docker and docker-compose in debian, `docker-ghost` for installing the prerequisites of the ghost package and running it using `docker-compose`)
5. [docker](./docker) (Here we define the ghost docker configuration files, we have the `docker-compose.yml` file which will start all of the required containers and will link them. We have our own custom nginx Dockerfile which adds `certs` and the ghost configuration)
