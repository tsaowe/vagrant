# -*- mode: ruby -*-
# vi: set ft=ruby :
require_relative 'vagrant_plugin_guest_denalios.rb'
$number_of_nodes = 3
$hostname_base = "max"
$vm_mem = "4096"
$vb_gui = false
$bridge_interface = "em1"
# IMPORTANT: The following dedicated MAC/IP pairs for Integration usage only
# Please check with sysadmin for ganranteed unique MAC.
$mac_addresses = ["087234233ff0", "086747234ff1", "085642235ff2","083267236ff3","083267237ff4","083267238ff5"]
$ip_address = ["192.168.100.240","192.168.100.241","192.168.100.242","192.168.100.243","192.168.100.244","192.168.100.245"]
# [10.10.30.50, 10.10.30.51, 10.10.30.52]
# eth0 is NAT to VM host
# eth1 is bridge to LAN
#$eth2_prefix = "75.75.0"
#$eth3_prefix = "40.40.0"

# the node number is added to start_at to get the ip suffix of eth2 and eth3
# for example i=1,start_at=100, (1+100) = 101, used in 75.75.0.101
#start_at = 100

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  config.vm.box = "xiangyang"
  # config.vm.box_url = 'http://192.168.100.97:8098/catalog/orcadt/denali-ubuntu-Mainstream' 
  # config.vm.box_version = ">=0.1.0"
  config.vm.provision "shell", run: "always", inline: "sudo route add default gw 192.168.100.1"
  (1..$number_of_nodes).each do |i|
    hostname = "%s%d" % [$hostname_base, i]
    config.vm.define hostname do |node|
      node.vm.provider "virtualbox" do |vb|
        vb.memory = $vm_mem
        vb.gui = $vb_gui
        (1..3).each do |port|
          hdd_file = "hdds/%s-hdd-%d.vdi" % [hostname, port]
          unless File.exist?(hdd_file)
            vb.customize ["createhd", "--filename", hdd_file, "--size", 4096]
          end
          vb.customize ["storageattach", :id, "--storagectl", "SATA Controller", "--port", "%d" % port, "--type", "hdd", "--medium", hdd_file]
        end
      end
      # Enable this for MAC-based static IP allocation
      #node.vm.network "public_network", :bridge => $bridge_interface, :mac => $mac_addresses[i - 1]
      node.vm.network "public_network", :bridge => $bridge_interface, :mac => $mac_addresses[i - 1], :ip => $ip_address[i - 1]
      #node.vm.network :private_network, :ip => $eth2_prefix+".#{i+start_at}"
      #node.vm.network :private_network, :ip => $eth3_prefix+".#{i+start_at}"
      node.vm.hostname = hostname
    end
  end
end
