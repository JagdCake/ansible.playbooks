### Directory structure
1. [local](./local) is used for playbooks that execute tasks on the host or my personal machine. Currently contains a single playbook for installing and setting up OpenVPN.
2. [remote](./remote) playbooks execute tasks on remote servers / virtual machines. Mainly used on a CentOS 7 VPS; most of the tasks work fine on CentOS 8 too. Playbooks prepended with `centos7` need some work, while the playbook which sets up an OpenVPN server is pretty much done.
