# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.ssh.forward_agent = true
  config.vm.synced_folder Dir.getwd, "/home/vagrant/ansible-mesos-playbook"

  # ubuntu
  config.vm.define 'ubuntu', primary: true do |c|
    c.vm.network "private_network", ip: "192.168.100.2"
    c.vm.box = "ubuntu/trusty64"
    c.vm.provision "shell" do |s|
      s.inline = "apt-get update -y; apt-get install ansible -y;"
      s.privileged = true
    end
    c.vm.provision "ansible" do |ansible|
        ansible.playbook = "playbook.yml"
        ansible.groups = { 'mesos_masters' => 'ubuntu', 'mesos_slaves' => 'ubuntu' }
    end
  end

  # centos:
  # config.vm.define 'centos' do |c|
  #   c.vm.network "private_network", ip: "192.168.100.3"
  #   c.vm.box = "chef/centos-6.5"
  #   c.vm.provision "shell" do |s|
  #     s.inline = "/bin/true"
      # s.privileged = true
    # end
  # end

end
