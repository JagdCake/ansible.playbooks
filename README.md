### Description
My [Ansible](https://www.ansible.com/) playbooks, used mainly for automating the set up and maintenance of a CentOS 7 VPS. I'm trying to move away from using [BASH shell scripts and manually running commands](https://github.com/JagdCake/docker.space_program), to make the process more robust and fault tolerant. The directories are for organizing playbooks based on the machine they are executed on.

### Current state
[localhost](./localhost) was used for practice while I was going through the [Ansible docs](https://docs.ansible.com/ansible/2.7/); it's not in use at the moment.

[vultr](./vultr) playbooks are what I'm focused on. Tested on a virtual machine and used on a production server, still have some things to work out.
