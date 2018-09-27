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

  config.vm.provision :hostmanager
  config.vm.provision :ansible do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "ansible/playbook.yml"
  end

  (1..1).each do |i|
    config.vm.define "master-#{i}" do |master|
      master.vm.hostname = "master-#{i}"
      master.vm.network :private_network, ip: "10.10.10.1#{i}"
      master.vm.provider :libvirt do |libvirt|
        libvirt.cpus = 1
        libvirt.memory = 2048
      end
    end
  end

  (1..3).each do |i|
    config.vm.define "worker-#{i}" do |worker|
      worker.vm.hostname = "broker-#{i}"
      worker.vm.network :private_network, ip: "10.10.10.2#{i}"
      worker.vm.provider :libvirt do |libvirt|
        libvirt.cpus = 1
        libvirt.memory = 2048
        libvirt.storage :file, :size => '50G'
      end
    end
  end

  (1..1).each do |i|
    config.vm.define "client-#{1}" do |client|
      client.vm.hostname = "client-#{i}"
      client.vm.network :private_network, ip: "10.10.10.3#{i}"
      client.vm.provider :libvirt do |libvirt|
        libvirt.cpus = 1
        libvirt.memory = 2048
      end
    end
  end

end
