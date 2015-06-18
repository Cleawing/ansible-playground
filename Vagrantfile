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
        vb.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
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
        vb.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
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
            "weave_nodes:children" => ["masters", "slaves"],
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
end
