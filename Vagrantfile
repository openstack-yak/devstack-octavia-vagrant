# -*- mode: ruby -*-
# vi: set ft=ruby :

require "vagrant-reload"
require "vagrant-openstack-plugin"

Vagrant.configure(2) do |config|

  config.vm.provider :openstack do |os|
    # User, password and other configuration
    os.openstack_auth_url     = ENV["OS_AUTH_URL"]
    os.username               = ENV["OS_USERNAME"]
    os.password               = ENV["OS_PASSWORD"]
    os.tenant_name            = ENV["OS_PROJECT_NAME"]
    os.openstack_network_url  = ENV["OS_NETWORK_URL"]
    os.openstack_image_url    = ENV["OS_IMAGE_URL"]

    # VM configuration
    os.flavor                 = "memory1-32"
    os.networks               = ["dilu_mgmt"]
    os.keypair_name           = "dilu"
    os.security_groups        = ["default-cleared", "ssh", "mosh"]
    os.volume_boot            = {
      image: "ubuntu-16.04-cloud",
      size: 50,
      delete_on_destroy: true
    }
  end

  config.vm.define "dilu-devstack-octavia-kvm" do |octavia|
    octavia.ssh.private_key_path = "~/.ssh/id_rsa"
    octavia.ssh.proxy_command = "ssh -W %h:%p -q bastion"
    octavia.ssh.forward_agent = true
    octavia.ssh.username = "ubuntu"

    octavia.vm.provision "shell", inline: "sudo apt-get -y install python-minimal"

    octavia.vm.provision "ansible" do |ansible|
      ansible.playbook = "playbook.yml"
    end
  end
end
