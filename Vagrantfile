# -*- mode: ruby -*-
# vi: set ft=ruby :

# This is a Vagrant to automatically provision a bootstrap node with a
# Chef server.
# See http://www.vagrantup.com/ for info on Vagrant.

$local_mirror = nil
#$local_mirror = "10.0.100.4"

if $local_mirror.nil?
  $repos_script = <<EOH
    echo "deb http://apt.opscode.com precise-0.10 main" > /etc/apt/sources.list.d/opscode.list
EOH
else
  $repos_script = <<EOH
    sed -i s/archive.ubuntu.com/#{$local_mirror}/g /etc/apt/sources.list
    sed -i s/security.ubuntu.com/#{$local_mirror}/g /etc/apt/sources.list
    sed -i s/^deb-src/\#deb-src/g /etc/apt/sources.list
    echo "deb http://#{$local_mirror}/chef precise-0.10 main" > /etc/apt/sources.list.d/opscode.list
EOH
end

Vagrant.configure("2") do |config|

  config.vm.define :bootstrap do |bootstrap|
    bootstrap.vm.hostname = "bcpc-bootstrap"

    bootstrap.vm.network :private_network, ip: "10.0.100.3", netmask: "255.255.255.0", adapter_ip: "10.0.100.2"
    bootstrap.vm.network :private_network, ip: "172.16.100.3", netmask: "255.255.255.0", adapter_ip: "172.16.100.2"
    bootstrap.vm.network :private_network, ip: "192.168.100.3", netmask: "255.255.255.0", adapter_ip: "192.168.100.2"

    bootstrap.vm.synced_folder "../", "/chef-bcpc-host"

    # set up repositories
    bootstrap.vm.provision :shell, :inline => $repos_script

    # since we are creating the server and the validation keys on this new
    # machine itself, we can't use Vagrant's built-in chef provisioning.
    bootstrap.vm.provision :shell, :inline => "/chef-bcpc-host/bootstrap_chef.sh --vagrant-local 10.0.100.3"
  end

  #config.vm.define :mirror do |mirror|
  #  mirror.vm.hostname = "bcpc-mirror-vagrant"
#
#    mirror.vm.network :private_network, ip: "10.0.100.4", netmask: "255.255.255.0", adapter_ip: "10.0.100.2"
#    mirror.vm.network :private_network, ip: "172.16.100.4", netmask: "255.255.255.0", adapter_ip: "172.16.100.2"
#    mirror.vm.network :private_network, ip: "192.168.100.4", netmask: "255.255.255.0", adapter_ip: "192.168.100.2"
#
#  end

  config.vm.box = "precise64"
  #config.vm.box_url = "http://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-vagrant-disk1.box"
  config.vm.box_url = "precise-server-cloudimg-amd64-vagrant-disk1.box"

  config.vm.provider :virtualbox do |vb|
     # Don't boot with headless mode
     vb.gui = true
     vb.name = "bcpc-bootstrap"
     vb.customize ["modifyvm", :id, "--nictype2", "82543GC"]
     vb.customize ["modifyvm", :id, "--memory", "1024"]
     #vb.customize ["modifyvm", :id, "--ioapic", "on"]
     #vb.customize ["modifyvm", :id, "--chipset", "ich9"]
   end

end
