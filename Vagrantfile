# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/focal64"
  config.vm.define "opensk"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Disable the default share of the current code directory. Doing this
  # provides improved isolation between the vagrant box and your host
  # by making sure your Vagrantfile isn't accessible to the vagrant box.
  # If you use this you may want to enable additional shared subfolders as
  # shown above.
  # config.vm.synced_folder ".", "/vagrant", disabled: true

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = true
  
    # Customize the amount of memory on the VM:
    vb.memory = "4096"

    vb.customize [
      "modifyvm", :id,
      "--vram", "256", # フルスクリーンモード用
      "--clipboard", "bidirectional", # クリップボード共有
      "--draganddrop", "bidirectional", # ドラッグアンドドロップ
      "--cpus", "4",
      "--ioapic", "on" # I/O APICを有効化
    ]
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y ubuntu-desktop
    # Optional: Install VirtualBox Guest Additions
    apt-get install -y virtualbox-guest-dkms virtualbox-guest-utils virtualbox-guest-x11
    # Start the graphical interface
    systemctl set-default graphical.target
    systemctl start graphical.target

    # Install build tools
    apt-get install -y build-essential curl wget git

    # Install Rust
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

    # Install Python
    apt-get install -y python3-pip python3-venv python3-dev

    # Install OpenSSL
    apt-get install -y libssl-dev pkg-config

    # nrfutil for OpenSK Dongle
    pip3 install nrfutil

    # llvm and gcc-arm-none-eabi if you want to use the upgradability feature.
    apt-get install -y llvm gcc-arm-none-eabi

    # Segger
    wget https://www.segger.com/downloads/jlink/JLink_Linux_x86_64.deb
    dpkg -i JLink_Linux_x86_64.deb

    # Clone Repositories
    mkdir -p ~/Work
    cd ~/Work
    git clone https://github.com/google/OpenSK.git

    # Additionally on Linux, you need to install a udev rule file to allow non-root users to interact with OpenSK devices. 
    cp rules.d/55-opensk.rules /etc/udev/rules.d/
    udevadm control --reload

    # OpenSK Setup
    cd OpenSK
    sh ./setup.sh
  SHELL
end
