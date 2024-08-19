create\_vm
==============

Create lab QEMU virtual machines.

Requirements
------------
todo

Role Variables
--------------

todo

| Variable | Default | Description |
| --- | --- | --- |
| rhsm\_download\_files | Empty list | List of files to download.  A non-exhaustive list of valid files is found in [vars/main.yml](vars/main.yml). |
| rhsm\_download\_directory | `/tmp` | Directory where the downloaded files will be written |
| rhsm\_download\_file\_mode | `0644` | File permissions (in octal form) to set on the downloaded files |
| rhsm\_download\_offline\_token | Empty string | Red Hat API offline token.  This variable should be overridden with the real offline token value stored in an Ansible vault or Tower credential, as it allows API access to the RHSM API with the permissions of the account holder.  A new token will need to be generated if it is not used at least once every 30 days.  See [Getting started with Red Hat APIs ](https://access.redhat.com/articles/3626371) for more information. |

See [defaults/main.yml](defaults/main.yml) for additional variables that normally don't need to be changed.

Dependencies
------------

None

Example Playbook
----------------

    ---
    - name: Create a test VM as the current user
      gather_facts: false
      become: false
      hosts:
        - localhost
      vars:
        lab:
          ssh_public_key: "ssh-ed25519 ..."
          name: linux
          network:
            name: default
            type: bridge
          hosts:
            - name: test-vm
              macaddr: 52:54:00:6c:3c:99
              ipaddr: 192.168.122.99
              libvirt_session: session
              disk:
                size_gb: 20
                root_fs: /dev/sda4
              memory: 4096
              cpu: 1
              image: rhel-9.4-x86_64-kvm.qcow2
      roles:
        - { role: create_vm }

    ...

License
-------

MIT
