VAGRANTFILE_VM_BOX_NAME = "precise64"
VAGRANTFILE_VM_BOX_URL = "http://files.vagrantup.com/precise64.box"
VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.vm.define :dev do |dev_config|
        dev_config.vm.box = VAGRANTFILE_VM_BOX_NAME
        dev_config.vm.box_url = VAGRANTFILE_VM_BOX_URL
        dev_config.vm.network :forwarded_port, guest: 80, host: 9080
        dev_config.vm.network :forwarded_port, guest: 443, host: 9443
        dev_config.vm.network :private_network, ip: "172.8.8.100"
        dev_config.vm.network :public_network, bridge: "wlan0"
        dev_config.ssh.forward_agent = true
        dev_config.vm.provider :virtualbox do |vb|
            vb.customize ["modifyvm", :id, "--memory", "2048"]
        end
        dev_config.vm.synced_folder ".", "/vagrant",
            disabled: true
        dev_config.vm.synced_folder ".", "/var/www/symfony.local.dev/releases/development",
            create: true,
            owner: "www-data", group: "www-data"
        dev_config.vm.provision "ansible" do |ansible|
            ansible.limit = 'all'
            ansible.playbook = "app/config/ansible/install_dev.yml"
            ansible.inventory_path = "app/config/ansible/hosts/development.ini"
            ansible.groups = {
                "webservers" => ["webhost"],
                "dbservers" => ["dbhost"]
            }
            ansible.extra_vars = {
                app_domain: "symfony.local.dev",
                sf2project_env: "dev",
                release_id: "development"
            }
            ansible.skip_tags = "git-clone,deploy-prod"
            ansible.verbose = 'vvvv'
        end
    end
    config.vm.define :web do |web_config|
        web_config.vm.box = VAGRANTFILE_VM_BOX_NAME
        web_config.vm.box_url = VAGRANTFILE_VM_BOX_URL
        web_config.vm.network :forwarded_port, guest: 80, host: 8080
        web_config.vm.network :forwarded_port, guest: 443, host: 8443
        web_config.vm.network :private_network, ip: "172.8.8.10"
        web_config.vm.network :public_network, bridge: "wlan0"
        web_config.ssh.forward_agent = true
        web_config.vm.provider :virtualbox do |vb|
            vb.customize ["modifyvm", :id, "--memory", "2048"]
        end
        web_config.vm.synced_folder ".", "/vagrant",
            disabled: true
        web_config.vm.provision "ansible" do |ansible|
            ansible.limit = 'all'
            ansible.playbook = "app/config/ansible/install_prod_web.yml"
            ansible.inventory_path = "app/config/ansible/hosts/staging.ini"
            ansible.groups = {
                "webservers" => ["webhost"],
                "dbservers" => ["dbhost"]
            }
            ansible.extra_vars = {
                app_domain: "symfony.local.test",
                sf2project_env: "prod",
                database_host: '172.8.8.20'
            }
            ansible.verbose = 'vvvv'
        end
    end
    config.vm.define :db do |db_config|
        db_config.vm.box = VAGRANTFILE_VM_BOX_NAME
        db_config.vm.box_url = VAGRANTFILE_VM_BOX_URL
        db_config.vm.network :forwarded_port, guest: 3306, host: 33306
        db_config.vm.network :private_network, ip: "172.8.8.20"
        db_config.ssh.forward_agent = true
        db_config.vm.provider :virtualbox do |vb|
            vb.customize ["modifyvm", :id, "--memory", "1024"]
        end
        db_config.vm.synced_folder ".", "/vagrant",
            disabled: true
        db_config.vm.provision "ansible" do |ansible|
            ansible.limit = 'all'
            ansible.playbook = "app/config/ansible/install_prod_db.yml"
            ansible.inventory_path = "app/config/ansible/hosts/staging.ini"
            ansible.groups = {
                "webservers" => ["webhost"],
                "dbservers" => ["dbhost"]
            }
            ansible.extra_vars = {
                app_host: '172.8.8.10'
            }
            ansible.verbose = 'vvvv'
        end
    end
end
