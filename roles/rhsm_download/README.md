rhsm\_download
==============

Vendored from https://github.com/jce-redhat/ansible-collection-rhsm and added additional checksums for newer software releases.

rhsm\_download - Download product images from the Red Hat Customer Portal using the RHSM API

This role allows you to download product files (including ISOs, VM images, install bundles, etc.) from the [Red Hat Customer Portal](https://access.redhat.com/downloads/) using the RHSM API.  You can only download files for which you have a valid subscription.

The role keeps a map of some common product images in [vars/main.yml](vars/main.yml) and their SHA256 checksums, as those checksums are used instead of file names when downloading an image using the API.  There are a vast number of files available for download, so only a few products are kept in the map (mainly the ones I download regularly for use in my home lab).  However, any available file can be downloaded if the checksum is known.

General information on using the RHSM API is available in the [Red Hat Knowledgebase](https://access.redhat.com/articles/3626371).

Requirements
------------

The role requires a valid Red Hat Customer Portal account and a valid subscription for any product file(s) to be downloaded.  An offline API token is also needed, which ideally will be stored in an Ansible vault.

Tokens can be generated in the [Red Hat Customer Portal](https://access.redhat.com/management/api). Click on the 'GENERATE TOKEN' button, copy the token output, and use it as the value for the variable `rhsm_download_offline_token` (this should be a vaulted variable). These tokens will expire after 30 days of inactivity, so if you get a '404 Unauthorized' error when running the rhsm\_download role, you may need to generate a new token and update your Ansible vault variable.

Role Variables
--------------

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
    - name: RHSM download
      hosts: localhost
      become: false

      vars_files:
        vars/vault.yml

      tasks:
        - name: Download setup tarball for Ansible Automation Platform 1.2.4
          include_role:
            name: jce_redhat.rhsm.rhsm_download
          vars:
            # these filenames must be the same as the keys used in
            # the rhsm_download_file_checksum dictionary
            rhsm_download_files:
              - ansible-automation-platform-setup-2.2.1-1.tar.gz

        - name: Download RHEL QCOW2 images to the default libvirt storage location
          include_role:
            name: jce_redhat.rhsm.rhsm_download
          vars:
            rhsm_download_directory: /var/lib/libvirt/images
            rhsm_download_files:
              - rhel-baseos-9.1-x86_64-kvm.qcow2
              - rhel-8.7-x86_64-kvm.qcow2
              - rhel-server-7.9-update-9-x86_64-kvm.qcow2
          become: true

        - name: Download a file not already listed in vars/main.yml
          include_role:
            name: jce_redhat.rhsm.rhsm_download
          vars:
            rhsm_download_files:
              - Satellite-6.12-rhel-8-x86_64.dvd.iso
            rhsm_download_file_checksum:
              Satellite-6.12-rhel-8-x86_64.dvd.iso: '36f67e2790067add3339c3df7ee0c02fcc970d18d20c9967d879ac039a49f393'

    ...

The Ansible vault file `vars/vault.yml` should contain the following variable:

    rhsm_download_offline_token: eyJhbGciOiJIUz...G4rdsFEsYMB-7A

License
-------

MIT
