## Setting up VMs on macOS using Virtual box:

- Download and install Virtual box: https://www.virtualbox.org/wiki/Downloads
- Download centos 7 VM image: https://www.linuxvmimages.com/images/centos-7/
- Import the centos 7 image into virtual box
- We can go with the default configuration for network cards, RAM etc.
- After the image is imported, go to network settings and add following network adapters:
    - Bridged adapter (with `en0` interface) (for communication between multiple VMs)
    - NAT adapter (for communication via host OS)
- Duplicate this image in order to create another VM
- Share host ssh keys with the VM so that Ansible is able to connect to VMs from host OS
- Disable the firewall on VMs using `systemctl stop firewalld` command as this would prevent location service cluster from forming. Alternatively, whitelist the VM IPs in firewall. 

## Running the ansible deployment on VMs

- Install ansible: https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible-on-macos
- Replace the ip address in `inventory.yml` with the ip address of VMs that are started (can be found using `ifconfig` command on VM)
- Replace the ip of seed node VM in `CLUSTER_SEEDS` env variable in `playbook.yml` 
- Copy the contents of `inventory.yml` to `/etc/ansible/hosts` 
- Run ansible playbook using command `ansible-playbook <path to playbook.yml> --extra-vars "@<path to deploy_versions.json>"`

Ansible playbook contains the task that are to be executed on the target nodes. This includes:
- Download and install coursier
- Install Java and setup path env
- Install and launch location service cluster using one VM as seed node
- Install and launch config service on one VM
- Install and launch agent app on both VMs

Host specific variables like `machine_name` are defined in the inventory file.

Variables for versions (`csw-version` and `esw_version`) are defined in the `deploy_versions.json` file.