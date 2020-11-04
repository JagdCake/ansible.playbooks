### Directory structure
1. [local](./local) is used for playbooks that execute tasks on the host or my personal machine. Contains a playbook for installing and setting up OpenVPN client and playbooks to install / remove packages after installing Solus / Fedora.
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
        - solus.openvpn : assign the EasyRSA location to a variable	TAGS: []
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
3. playbook [local/solus.packages.yml](./local/solus.packages.yml)
    - tasks:
        - solus.packages : remove unnecessary default packages	TAGS: []
        - solus.packages : update stable repository	TAGS: []
        - solus.packages : upgrade all installed packages	TAGS: []
        - solus.packages : install system packages	TAGS: []
        - solus.packages : install base development tools	TAGS: []
        - solus.packages : install work tools	TAGS: []
        - solus.packages : install media apps	TAGS: []
        - solus.packages : install python packages	TAGS: []
        - solus.packages : install Node.js packages	TAGS: []
        - solus.packages : install Haskell	TAGS: []
        - solus.packages : update stack package index	TAGS: []
        - solus.packages : install Haskell libraries	TAGS: []
        - solus.packages : install Ruby gems	TAGS: []
    - variables (set in the role's vars/main.yml):
        - items_to_remove: "list of default packages to remove"
        - system: "list of system packages to install; e.g. alacritty,
          ripgrep, rclone"
        - tools: "list of tools to install; e.g. git,
          neovim, tmux"
        - media: "list of media application packages to install; e.g.
          mpv, steam, lutris"
        - python: "list of python packages to install with pip3'
        - nodejs: "list of Node.js packages to install (globally) using
          npm"
        - haskell: "list of Haskell packages to install using stack"
        - ruby: "list of Ruby gems to install"
4. playbook: [remote/centos8.openvpn.yml](./remote/centos8.openvpn.yml), **depends on** [remote/centos7.set_up.yml](./remote/centos7.set_up.yml) and [local/solus.openvpn.yml](./local/solus.openvpn.yml)
    - tasks:
        - centos8.openvpn : install OpenVPN	TAGS: []
        - centos8.openvpn : align the server timezone with the client timezone	TAGS: []
        - centos8.openvpn : download EasyRSA	TAGS: []
        - centos8.openvpn : install tar	TAGS: []
        - centos8.openvpn : unarchive EasyRSA	TAGS: []
        - centos8.openvpn : assign the EasyRSA location to a variable	TAGS: []
        - centos8.openvpn : delete the EasyRSA archive	TAGS: []
        - centos8.openvpn : initialize public key infrastructure	TAGS: []
        - centos8.openvpn : install pexpect	TAGS: []
        - centos8.openvpn : generate OpenSSL randfile	TAGS: []
        - centos8.openvpn : create certificate authority	TAGS: []
        - centos8.openvpn : transport client's certificate request	TAGS: []
        - centos8.openvpn : import client's certificate request	TAGS: []
        - centos8.openvpn : sign client's certificate request	TAGS: []
        - centos8.openvpn : transfer signed certificate to client	TAGS: []
        - centos8.openvpn : transfer CA certificate to client	TAGS: []
        - centos8.openvpn : generate certificate request	TAGS: []
        - centos8.openvpn : sign server's certificate request	TAGS: []
        - centos8.openvpn : transfer server config file to the OpenVPN server dir	TAGS: []
        - centos8.openvpn : generate Diffie-Hellman parameters file (takes some time)	TAGS: []
        - centos8.openvpn : archive server config dependencies for transfer	TAGS: []
        - centos8.openvpn : unarchive server config dependencies	TAGS: []
        - centos8.openvpn : delete dependencies archive	TAGS: []
        - centos8.openvpn : generate static encryption key	TAGS: []
        - centos8.openvpn : allow OpenVPN through the firewall	TAGS: []
        - centos8.openvpn : enable masquerading for the public zone	TAGS: []
        - centos8.openvpn : route connections to the OpenVPN subnet	TAGS: []
        - centos8.openvpn : restore the default target of the public zone	TAGS: []
        - centos8.openvpn : reload firewalld	TAGS: []
        - centos8.openvpn : enable IP forwarding	TAGS: []
    - variables (set in the role's vars/main.yml):
        - my_timezone: "local machine timezone, e.g. Europe/Helsinki; check your timezone with timedatectl"
        - username: "local (client) machine username"
        - ca_key_passphrase: "password to set up a Certificate Authority on the server"
        - client_entity_name: "the name used to create a certificate for the client machine, same as "entity_name" from the above playbook, e.g. jc-desktop"
        - openvpn_client_dir: "absolute path to the OpenVPN directory on the client machine"
        - vpn_subnet: "IP address from the 10.0.0.0/8 netblock, e.g. 10.66.77.0"
        - dns1_IP: "DNS server the client will use to connect to OpenVPN, e.g. 1.1.1.1"
        - dns2_IP: "e.g. 1.0.0.1"
        - tls_key_name: "has to be the same as the "tls_key_name" variable  from the above playbook, e.g. jc-vpn"
        - server_entity_name: "common name used to create a certificate for the server, e.g. jc-server"
        - pem_passphrase: "same password used to create a client certificate with the playbook above"
5. playbook: [local/fedora.packages.yml](./local/fedora.packages.yml)
    - tasks:
        - fedora.packages : remove unnecessary default packages	TAGS: []
        - fedora.packages : upgrade all installed packages	TAGS: []
        - fedora.packages : install required packages for a minimal KDE install	TAGS: []
        - fedora.packages : enable the free and nonfree RPM Fusion repositories	TAGS: []
        - fedora.packages : install system packages	TAGS: []
        - fedora.packages : install work packages	TAGS: []
        - fedora.packages : install media apps	TAGS: []
        - fedora.packages : install python packages	TAGS: []
        - fedora.packages : create directory for global NPM packages	TAGS: []
        - fedora.packages : configure NPM to use the created directory to install global packages	TAGS: []
        - fedora.packages : install Node.js packages	TAGS: []
        - fedora.packages : download stack (Haskell package tool)	TAGS: []
        - fedora.packages : install stack	TAGS: []
        - fedora.packages : update stack's package index	TAGS: []
        - fedora.packages : install Haskell libraries	TAGS: []
        - fedora.packages : install Ruby gems	TAGS: []
        - fedora.packages : install Rust crates	TAGS: []
    - variables (set in the role's vars/main.yml):
        - packages_to_remove: "list of default packages to remove"
        - system: "list of system packages to install; e.g. alacritty,
          ripgrep, rclone"
        - tools: "list of tools to install; e.g. git,
          neovim, tmux"
        - media: "list of media application packages to install; e.g.
          mpv, steam, lutris"
        - python: "list of Python packages to install with pip3'
        - nodejs: "list of Node.js packages to install (globally) using
          npm"
        - haskell: "list of Haskell packages to install using stack"
        - ruby: "list of Ruby gems to install"
        - rust: "list of Cargo crates to install"
