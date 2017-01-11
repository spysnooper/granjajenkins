# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
VAGRANT_STORAGE_POOL = "maquinas_virtuales-1"

Vagrant.require_version ">= 1.8.0"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  vagrant_cluster_config = {
    "box_name"  => ENV['VAGRANT_BOX_NAME'] || "centos/7",
    "num_hosts" => ENV['VAGRANT_NUM_HOSTS'] || 3,
    "master_node_mem"  => ENV['VAGRANT_MASTER_NODE_MEM'] || 512,
    "worker_node_mem"  => ENV['VAGRANT_WORKER_NODE_MEM'] || 512
  }

# Cofiguramos la plantilla a utilizar
  config.vm.box = vagrant_cluster_config["box_name"]

# Disabling the default /vagrant share
  config.vm.synced_folder ".", "/vagrant", disabled: true

# 1: Jenkins Master Node (jk_master)
# 2: Jenkins Worker Node 1 (jk_worker1)
# 3: Jenkins Worker Node 2 (jk_worker2)

  N = (vagrant_cluster_config['num_hosts']).to_i
  
  (1..N).each do |i|
    if i == 1
# master node
      vm_name = "jk_master"
      vm_memory = vagrant_cluster_config['master_node_mem']
    else
# webserver nodes
      vm_name = "jk_worker#{i-1}"
      vm_memory = vagrant_cluster_config['worker_node_mem']
    end		# end if

    config.vm.define "#{vm_name}" do |node|
      node.vm.hostname = "#{vm_name}"
      if Vagrant.has_plugin?("vagrant-libvirt")
        node.vm.provider :libvirt do |domain|
          domain.uri = 'qemu+unix:///system'
          domain.driver = 'kvm'
          domain.storage_pool_name = VAGRANT_STORAGE_POOL
          domain.memory = "#{vm_memory}".to_i
          domain.cpus = 1
        end	# end domain
      end	# end if
    end		# end node
  end 		# end i
  config.vm.provision :ansible do |ansible|
    ansible.groups = {
      "masters" => ["jk_master"],
      "workers" => ["jk_worker1", "jk_worker2"],
      "servers:children" => ["masters", "workers"]
    }
    ansible.playbook = "./ansible/playbook.yml"
    #ansible.verbose = 'v'
  end		# end ansible
end 		# end config
