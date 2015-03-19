This Ansible playbook is designed to simplify deployment of projects either on Vagrant or local machine.

Deploying to vagrant:
--------------------

1) Create PROJECT_ROOT folder, for example /www/bo.v6.rbc.vagrant
2) Clone this repo to PROJECT_ROOT/provisioning/ folder.
3) Create Vagrantfile with content similar to the following:

    # -*- mode: ruby -*-
    # vi: set ft=ruby :
    
    Vagrant.configure(2) do |config|
        config.vm.box = "ubuntu/trusty64"
    
        config.vm.network :private_network, type: :dhcp
        config.vm.hostname = "rbc6-bo"
        config.vm.synced_folder "/www", "/www"

        config.vm.provision "ansible" do |ansible|
            ansible.playbook = "provisioning/playbook.yml"
            ansible.verbose = 'vv',
            ansible.extra_vars = {
                project_host: 'bo.v6.rbc.vagrant',
                project_path: '/www/bo.v6.rbc.vagrant',
                user: {
                    full_name: 'Full Name',
                    email: 'email@example.com',
                    name: 'username',
                    password: '***'
                }
            }
        end
    end


4) Run vagrant provision


Deploying to local host:
-----------------------

1) Create PROJECT_ROOT folder, for example /www/bo.v6.rbc.local
2) Clone this repo to PROJECT_ROOT/provisioning/ folder.
3) Create PROJECT_ROOT/inventory file with the following content:

    [webservers]
    localhost ansible_connection=local

4) Create PROJECT_ROOT/group_vars file with content similar to the following:

    ---
    project_host: bo.v6.rbc.local
    project_path: /www/bo.v6.rbc.local
    user:
      full_name: Full Name
      email: email@example.com
      name: username
      password: ***

5) Run the following command:

    ansible-playbook -vvv --ask-sudo-pass -i provisioning/inventory provisioning/playbook.yml