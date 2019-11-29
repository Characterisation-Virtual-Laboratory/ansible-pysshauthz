# Installing and Configuring pysshauthz Using ansible-pysshauthz

This playbook will configure and install pysshauthz.
It contains 4 separate roles:
1. [setup-server](roles/setup-server/README.md)
2. [setup-nginx](roles/setup-nginx/README.md)
3. [setup-pysshauthz](roles/setup-pysshauthz/README.md)
4. [configure-pysshauthz-sites](roles/configure-pysshauthz-sites/README.md)

More information on pysshauthz [here](https://gitlab.erc.monash.edu.au/hpc-team/pysshauthz)

## Requirement
* NECTAR VM running Ubuntu 18.04 (This playbook is tested with Ubuntu 18.04)
* Ansible >= 2.8
* Playbook and roles from this repository

## How to Use this Playbook
### Setup and Install pysshauthz
1. Use git to clone this repository locally: 
```
git clone https://github.com/Characterisation-Virtual-Laboratory/ansible-pysshauthz.git
```
2. Change into the directory: 
```
cd ansible-pysshauthz
```
3. Create an inventory file (see `inventory.example`) with the IP address or hostname of the VM where pysshauthz is going to be installed
4. Create  `group_vars/all` (see `group_vars/all.example`) with the details for `ansible_user`, `private_keys`, `default_url` for pysshauthz, and email address
5. Make sure the URL for the site is [configured in the DNS](roles/setup-nginx/README.md)
6. Copy `roles/configure-pysshaythz-sites/vars/main.yml.example` to `roles/configure-pysshaythz-sites/vars/main.yml`
7. Name the sites `roles/configure-pysshaythz-sites/vars/main.yml`. `Sites` is the list of sites CA that pysshauthz signs for the different clusters Strudel Web connects to.
7. Run the command below to run all the roles in the playbook
``` 
ansible-playbook -i inventory play.yml
```

This playbook uses nginx and LetsEncrypt's Certbot to handle the web service proxy and SSL certificate.
To use a different webservice and certificate service, you can setup the required services and certificates first and run `setup-pysshauthz` and `configure-pysshauthz-sites` roles afterwards.
