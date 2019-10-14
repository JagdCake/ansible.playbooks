### Directory structure
1. [local](./local) is used for playbooks that execute tasks on the host or my personal machine. Currently contains a single playbook for installing and setting up OpenVPN.
2. [remote](./remote) playbooks execute tasks on remote servers / virtual machines. Mainly used on a CentOS 7 VPS; most of the tasks work fine on CentOS 8 too. Playbooks prepended with `centos7` need some work, while the playbook which sets up an OpenVPN server is pretty much done.

### Playbook overview
1. playbook: [remote/centos7.set_up.yml](./remote/centos7.set_up.yml)
    - tasks:
        - centos7.set_up : create a new user	TAGS: []
        - centos7.set_up : give the new user root privileges	TAGS: []
        - centos7.set_up : set up authorized keys	TAGS: [security, ssh]
        - centos7.set_up : update SSH config	TAGS: [security, ssh]
        - centos7.set_up : deactivate short moduli	TAGS: [security, ssh]
        - centos7.set_up : make sure firewalld is running and enabled	TAGS: [security]
        - centos7.set_up : remove SSH access from the public zone	TAGS: [security]
        - centos7.set_up : allow access to SSH only from my IP	TAGS: [security]
        - centos7.set_up : drop (no rejection reply) all non-whitelisted connections to the SSH service	TAGS: [security]
        - centos7.set_up : restore the ability to ping (broken by the previous task)	TAGS: [security]
        - centos7.set_up : allow HTTP traffic	TAGS: [security]
        - centos7.set_up : allow HTTPS traffic	TAGS: [security]
        - centos7.set_up : upgrade all packages	TAGS: [upgrade_packages]
        - centos7.set_up : install release package "Extra Packages for Enterprise Linux"	TAGS: [fail2ban, security]
        - centos7.set_up : install fail2ban	TAGS: [fail2ban, security]
        - centos7.set_up : start fail2ban	TAGS: [fail2ban, security]
        - centos7.set_up : update fail2ban config	TAGS: [fail2ban, security]
        - centos7.set_up : create jail log file	TAGS: [fail2ban, security]
        - centos7.set_up : use Cloudflare's DNS	TAGS: []
        - centos7.set_up : reboot the server	TAGS: []
    - variables (set in the role's vars/main.yml):
        - username: "name of user to create on the server"
        - password: "password for the above user"
        - local_user: "local machine username"
        - static_ip: "IP (in CIDR notation, e.g. 10.39.25.151/24) of the local machine you want to allow SSH access from"
2. playbook: [local/solus.openvpn.yml](./local/solus.openvpn.yml)
    - tasks:
        - solus.openvpn : install OpenVPN	TAGS: []
        - solus.openvpn : download EasyRSA	TAGS: []
        - solus.openvpn : unarchive EasyRSA	TAGS: []
        - solus.openvpn : delete the EasyRSA archive	TAGS: []
        - solus.openvpn : initialize public key infrastructure	TAGS: []
        - solus.openvpn : install pexpect	TAGS: []
        - solus.openvpn : generate certificate request	TAGS: []
        - solus.openvpn : create a directory for OpenVPN	TAGS: []
        - solus.openvpn : transfer client config file to OpenVPN directory	TAGS: []
        - solus.openvpn : transfer client key file to OpenVPN directory	TAGS: []
    - variables (set in the role's vars/main.yml):
        - username: "local machine username"
        - entity_name: "common name used to create a certificate for the client, e.g. jc-desktop"
        - pem_passphrase: "password used to create a certificate for the client"
        - openvpn_server_ip: "the address of the remote machine you're going to use as a VPN server"
        - openvpn_client_dir: "absolute path to a directory (on your machine) you want to use for OpenVPN configuration, certs and keys"
        - tls_key_name: "name for key that is going to be generated on the OpenVPN server with playbook below, e.g. jc-vpn"
