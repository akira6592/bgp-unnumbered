Vagrant.configure(2) do |config|

  config.vbguest.auto_update = false

  # config.vm.provider "virtualbox" do |vb|
  #   vb.memory = 512
  # end

  #########################################################################
  ## Switches

  # spine01
  config.vm.define "spine01" do |device|
    device.vm.box = "CumulusCommunity/cumulus-vx"
    device.vm.hostname = "spine01"
    config.vm.provider "virtualbox" do |vm|
      vm.memory = 512
    end
    device.vm.network "private_network", virtualbox__intnet: "nw101", auto_config: false    # swp1
    device.vm.network "private_network", virtualbox__intnet: "nw102", auto_config: false    # swp2
    device.vm.network "forwarded_port", id: "ssh", guest: 22, host: 9021

    device.vm.provision :file, source: "provision/frr_spine01.conf", destination: "frr.conf"
    device.vm.provision :shell, inline: <<-SHELL
      sudo cp /home/vagrant/frr.conf /etc/frr/frr.conf 
      sudo sed -i 's/zebra=no/zebra=yes/' /etc/frr/daemons
      sudo sed -i 's/bgpd=no/bgpd=yes/' /etc/frr/daemons
      sudo systemctl restart frr
    SHELL
  end
  
  # spine02
  config.vm.define "spine02" do |device|
    device.vm.box = "CumulusCommunity/cumulus-vx"
    device.vm.hostname = "spine02"
    config.vm.provider "virtualbox" do |vm|
      vm.memory = 512
    end
    device.vm.network "private_network", virtualbox__intnet: "nw201", auto_config: false    # swp1
    device.vm.network "private_network", virtualbox__intnet: "nw202", auto_config: false    # swp2
    device.vm.network "forwarded_port", id: "ssh", guest: 22, host: 9022
    
    device.vm.provision :file, source: "provision/frr_spine02.conf", destination: "frr.conf"
    device.vm.provision :shell, inline: <<-SHELL
      sudo cp /home/vagrant/frr.conf /etc/frr/frr.conf 
      sudo sed -i 's/zebra=no/zebra=yes/' /etc/frr/daemons
      sudo sed -i 's/bgpd=no/bgpd=yes/' /etc/frr/daemons
      sudo systemctl restart frr
    SHELL
  end

  # leaf01
  config.vm.define "leaf01" do |device|
    device.vm.box = "CumulusCommunity/cumulus-vx"
    device.vm.hostname = "leaf01" 
    config.vm.provider "virtualbox" do |vm|
      vm.memory = 512
    end
    device.vm.network "private_network", virtualbox__intnet: "nw101", auto_config: false    # swp1
    device.vm.network "private_network", virtualbox__intnet: "nw201", auto_config: false    # swp2
    device.vm.network "private_network", virtualbox__intnet: "nw001", auto_config: false    # swp3
    device.vm.network "forwarded_port", id: "ssh", guest: 22, host: 9011

    device.vm.provision :file, source: "provision/frr_leaf01.conf", destination: "frr.conf"
    device.vm.provision :shell, inline: <<-SHELL
      sudo cp /home/vagrant/frr.conf /etc/frr/frr.conf 
      sudo sed -i 's/zebra=no/zebra=yes/' /etc/frr/daemons
      sudo sed -i 's/bgpd=no/bgpd=yes/' /etc/frr/daemons
      sudo systemctl restart frr
    SHELL
  end

  # leaf02
  config.vm.define "leaf02" do |device|
    device.vm.box = "CumulusCommunity/cumulus-vx"
    device.vm.hostname = "leaf02"
    config.vm.provider "virtualbox" do |vm|
      vm.memory = 512
    end
    device.vm.network "private_network", virtualbox__intnet: "nw102", auto_config: false    # swp1
    device.vm.network "private_network", virtualbox__intnet: "nw202", auto_config: false    # swp2
    device.vm.network "private_network", virtualbox__intnet: "nw002", auto_config: false    # swp3
    device.vm.network "forwarded_port", id: "ssh", guest: 22, host: 9012

    device.vm.provision :file, source: "provision/frr_leaf02.conf", destination: "frr.conf"
    device.vm.provision :shell, inline: <<-SHELL
      sudo cp /home/vagrant/frr.conf /etc/frr/frr.conf 
      sudo sed -i 's/zebra=no/zebra=yes/' /etc/frr/daemons
      sudo sed -i 's/bgpd=no/bgpd=yes/' /etc/frr/daemons
      sudo systemctl restart frr
    SHELL
  end

  #########################################################################
  ## Servers

  # sv01 under leaf01
  config.vm.define "sv01" do |device|
    device.vm.box = "centos/8"
    device.vm.hostname = "sv01" 
    device.vm.network "private_network", ip: "172.16.1.1", virtualbox__intnet: "nw001"
    device.vm.network "private_network", virtualbox__intnet: "nw001"
    device.vm.network "forwarded_port", id: "ssh", guest: 22, host: 9001
    device.vm.provision :shell, inline: <<-SHELL
      # sudo ip route add 10.0.0.0/24 via 172.16.1.254 dev eth1
      # sudo ip route add 172.16.0.0/16 via 172.16.1.254 dev eth1
      sudo nmcli connection modify "System eth1" +ipv4.routes "172.16.2.0/24 172.16.1.254"
      sudo nmcli connection modify "System eth1" +ipv4.routes "10.0.0.0/24 172.16.1.254"
      sudo nmcli connection down "System eth1"
      sudo nmcli connection up "System eth1"
    SHELL
  end

  # sv02 under leaf02
  config.vm.define "sv02" do |device|
    device.vm.box = "centos/8"
    device.vm.hostname = "sv02" 
    device.vm.network "private_network", ip: "172.16.2.1", virtualbox__intnet: "nw002"
    device.vm.network "private_network", virtualbox__intnet: "nw002"
    device.vm.network "forwarded_port", id: "ssh", guest: 22, host: 9002
    device.vm.provision :shell, inline: <<-SHELL
      # sudo ip route add 10.0.0.0/24 via 172.16.2.254 dev eth1
      # sudo ip route add 172.16.0.0/16 via 172.16.2.254 dev eth1
      sudo nmcli connection modify "System eth1" +ipv4.routes "172.16.1.0/24 172.16.2.254"
      sudo nmcli connection modify "System eth1" +ipv4.routes "10.0.0.0/24 172.16.2.254"
      sudo nmcli connection down "System eth1"
      sudo nmcli connection up "System eth1"
    SHELL
  end
end
