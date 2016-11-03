# -*- mode: ruby -*-
# vi: set ft=ruby :

require "vagrant-reload"
require "vagrant-openstack-provider"
require "yaml"

# precursor: load configuration from yaml file
# see:
#   - http://stackoverflow.com/questions/16708917/how-do-i-include-variables-in-my-vagrantfile
#   - http://blog.scottlowe.org/2014/10/22/multi-machine-vagrant-with-yaml/
#   - http://blog.scottlowe.org/2016/01/14/improved-way-yaml-vagrant/


Vagrant.configure(2) do |config|

  config.vm.provider :openstack do |os|
    # User, password and other configuration
    #  be sure to `source openrc` before running vagrant, so these are available;
    os.openstack_auth_url     = ENV["OS_AUTH_URL"]
    os.username               = ENV["OS_USERNAME"]
    os.password               = ENV["OS_PASSWORD"]
    os.tenant_name            = ENV["OS_TENANT_NAME"]
    os.openstack_network_url  = ENV["OS_NETWORK_URL"]
    os.openstack_image_url    = ENV["OS_IMAGE_URL"]

    # VM configuration
    os.flavor                 = "memory1-32"
    os.networks               = ["yak-dev1"]
    os.keypair_name           = "yarko"
    os.security_groups        = ["default-cleared", "ssh", "mosh"]
    os.volume_boot            = {
      image: "ubuntu-16.04-cloud",
      size: 50,
      delete_on_destroy: true
    }
  end

  config.vm.define "yak-devstack-octavia-kvm" do |octavia|
    # when running on your workstation, these are
    #   are picked up from your ~/.ssh/config file;
    # you will need these, however, to operate on a running system
    octavia.ssh.private_key_path = "~/.ssh/id_rsa"
    octavia.ssh.proxy_command = "ssh -W %h:%p -q cloud1"
    octavia.ssh.forward_agent = true
    octavia.ssh.username = "ubuntu"

    # to ensure python2 installation for ansible,  if needed
    # NOTE: as of 2016.11.01, ansible 2.2.0, ansible works w/ python3;
    #  - uncomment this is you have problems
    #octavia.vm.provision "shell", inline: "sudo apt-get -y install python-minimal"

    octavia.vm.provision "ansible" do |ansible|
      ansible.playbook = "playbook.yml"
    end
  end
end
