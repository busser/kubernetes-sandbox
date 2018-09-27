# -*- mode: ruby *-*
# vi: set ft=ruby

if Vagrant::VERSION == '1.8.5'
  ui = Vagrant::UI::Colored.new
  ui.error 'Unsupported Vagrant Version: 1.8.5'
  ui.error 'Version 1.8.5 introduced an SSH key permissions bug, please upgrade to version 1.8.6+'
  ui.error ''
end

Vagrant.configure("2") do |config|
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.ssh.insert_key = false
  config.vm.box_check_update = false

  config.vm.box = "centos/7"

  config.vm.provider :libvirt do |libvirt|
    libvirt.driver = "kvm"
    libvirt.uri = "qemu:///system"
  end

  #config.vm.provision :hostmanager

  cluster = {
    "master-1" => { :ip => "10.10.10.11", :cpus => 2, :memory => 2048 },
    "worker-1" => { :ip => "10.10.10.21", :cpus => 1, :memory => 2048, :disk => "50G" },
    "worker-2" => { :ip => "10.10.10.22", :cpus => 1, :memory => 2048, :disk => "50G" },
    "worker-3" => { :ip => "10.10.10.23", :cpus => 1, :memory => 2048, :disk => "50G" },
    "client-1" => { :ip => "10.10.10.31", :cpus => 1, :memory => 2048 },
  }

  cluster.each_with_index do | (hostname, specs), i |
    config.vm.define hostname do |node|

      node.vm.hostname = hostname
      node.vm.network :private_network, ip: specs[:ip]
      node.vm.provider :libvirt do |libvirt|
        libvirt.cpus = specs[:cpus]
        libvirt.memory = specs[:memory]
        if specs.key?(:disk)
          libvirt.storage :file, :size => specs[:disk]
        end
      end

      # run Ansible playbook only once for all nodes
      if i == 0
        node.vm.provision :ansible do |ansible|
          ansible.compatibility_mode = "2.0"
          ansible.limit = "all"
          ansible.galaxy_role_file = "ansible/requirements.yml"
          ansible.playbook = "ansible/playbook.yml"
        end
      end
    end
  end

end
