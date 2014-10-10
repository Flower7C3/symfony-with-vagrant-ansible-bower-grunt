TOC:
===============================

1. [Prepare development environment with Vagrant](#markdown-header-prepare-development-environment-with-vagrant)
2. [Prepare staging environment with Vagrant](#markdown-header-prepare-staging-environment-with-vagrant)
3. [Ansible example commands](#markdown-header-ansible-example-commands)

-------------------------------

Prepare development environment with Vagrant
===============================

1. Install [Virtualbox][virtualbox], [Vagrant][vagrant] and [Ansible][ansible].
2. Create some project directory and clone this repository.
3. Run `vagrant up dev` and go make some coffee (installation process will take a while).
4. If everything is ok just [open app][virtual-dev].

-------------------------------

Prepare staging environment with Vagrant
===============================

1. Install [Virtualbox][virtualbox], [Vagrant][vagrant] and [Ansible][ansible].
2. Create some project directory and clone this repository.
3. Install servers with `vagrant up web` and `vagrant up db`.
4. Copy content of *.pub* file from Your home driectory to Bitbucket [deployment keys](/panel-php/admin/deploy-keys), run deployment script `ansible-playbook -i app/config/ansible/hosts/testing.ini app/config/ansible/deploy.yml -vvvv` and go make some coffee (process will take a while).
5. If everything is ok just  [open app][virtual-web].

-------------------------------

Ansible example commands
===============================

* Install webservers on staging env:
```
ansible-playbook app/config/ansible/install_prod_web.yml\
 -i app/config/ansible/hosts/staging.ini\
 --extra-vars "database_host=172.8.8.20 app_domain=symfony.local.test sf2project_env=prod"
```

* Install dbservers on staging env:
```
ansible-playbook app/config/ansible/install_prod_db.yml\
 -i app/config/ansible/hosts/staging.ini\
 --extra-vars "app_host=172.8.8.10"
```

* Deploy app on staging env:
```
ansible-playbook app/config/ansible/sf2deploy.yml\
 -i app/config/ansible/hosts/staging.ini\
 --tags "sf2deploy"\
 --extra-vars "app_domain=symfony.local.test sf2project_env=prod release_id=`date +%Y%m%d%H%M%S`"
```

* Init app data on staging env:
```
ansible-playbook app/config/ansible/sf2init.yml\
 -i app/config/ansible/hosts/staging.ini\
 --tags "sf2init"\
 --skip-tags "sf2fixtures"
```

* Rollback app on staging env:
```
ansible-playbook app/config/ansible/sf2rollback.yml\
 -i app/config/ansible/hosts/staging.ini\
 --tags "sf2rollback"
```

* Deploy app on development env:
```
ansible-playbook app/config/ansible/sf2deploy.yml\
 -i app/config/ansible/hosts/development.ini\
 --tags "sf2deploy"\
 --skip-tags "git-clone"\
 --extra-vars "app_domain=symfony.local.dev sf2project_env=dev release_id=development"
```

* Availible extra params (find out more in [Ansible docs][ansibledocs]):
    * To run as *vagrant* user with its private key add on the end on command: `--user vagrant --private-key=~/.vagrant.d/insecure_private_key`
    * To run as *root* with password add: `--user root --ask-pass`
    * To increase Ansible's verbosity to obtain detailed logging:
        * `-v` verbose mode
        * `-vv`
        * `-vvv` more
        * `-vvvv` connection debugging

-------------------------------


[vagrant]: https://www.vagrantup.com/ "Development environments made easy"
[virtualbox]: https://www.virtualbox.org/ "VirtualBox is a powerful x86 and AMD64/Intel64 virtualization"
[ansible]: http://www.ansible.com/home "Ansible is Simple IT Automation"
[ansibledocs]: http://docs.ansible.com/index.html
[virtual-web]: http://localhost:8080/
[virtual-dev]: http://localhost:9080/app_dev.php
