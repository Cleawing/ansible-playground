# -*- mode: ruby -*-
# vi: set ft=ruby :

$master_num_instances=1
$slave_num_instances=1
$master_instance_name_prefix = "master"
$slave_instance_name_prefix = "slave"
$vm_gui = false
$vm_memory = 512
$vm_cpus = 1

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/vivid64"
  config.ssh.insert_key = false
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"
  
  (1..$master_num_instances).each do |i|
    config.vm.define vm_name = "%s-%02d" % [$master_instance_name_prefix, i] do |cfg|
      cfg.vm.provider :virtualbox do |vb|
        vb.gui = $vm_gui
        vb.memory = $vm_memory
        vb.cpus = $vm_cpus
      end

      ip = "172.17.8.#{i+100}"
      cfg.vm.network :private_network, ip: ip
      
      cfg.vm.provision "shell", inline: "sudo hostnamectl set-hostname '#{vm_name}'"
    end
  end
  
  (1..$slave_num_instances).each do |i|
    config.vm.define vm_name = "%s-%02d" % [$slave_instance_name_prefix, i] do |cfg|
      cfg.vm.provider :virtualbox do |vb|
        vb.gui = $vm_gui
        vb.memory = $vm_memory
        vb.cpus = $vm_cpus
      end

      ip = "172.17.8.#{i+$master_num_instances+100}"
      cfg.vm.network :private_network, ip: ip
      
      cfg.vm.provision "shell", inline: "sudo hostnamectl set-hostname '#{vm_name}'"
      
      if vm_name == "%s-%02d" % [$slave_instance_name_prefix, $slave_num_instances]
        cfg.vm.provision "ansible" do |ansible|
          ansible.limit = 'all'
          ansible.groups = {
            "masters" => (1..$master_num_instances).map { |i| "%s-%02d" % [$master_instance_name_prefix, i] },
            "slaves" => (1..$slave_num_instances).map { |i| "%s-%02d" % [$slave_instance_name_prefix, i] },
            "all_groups:children" => ["masters", "slaves"],
            "zookeeper_nodes:children" => ["masters"],
            "consul_masters:children" => ["masters"],
            "consul_slaves:children" => ["slaves"],
            "mesos_masters:children" => ["masters"],
            "mesos_slaves:children" => ["slaves"]
          }
          ansible.sudo = true
          ansible.verbose = "v"
          ansible.playbook = "provisioning/cluster.yml"
        end
      end 
    end
  end

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y apache2
  # SHELL
end
