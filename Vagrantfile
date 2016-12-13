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
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "debian/jessie64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

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

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
    vb.memory = "256"
    # Customize USB settings for connecting iOS device for testbed
    vb.customize ['modifyvm', :id, '--usb', 'on']
    vb.customize ["modifyvm", :id, "--usbehci", "on"]
    vb.customize ["usbfilter", "add", "0", "--target", :id, 
      "--name", "iPhone",
      "--vendorid", '0x05ac', 
      "--productid", '0x12a8']
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  $prereqsteps = <<-PREREQ_SCRIPT
    apt-get --assume-yes update
    apt-get --assume-yes upgrade
    apt-get --assume-yes install autoconf build-essential cython doxygen git-core \
                                 libcurl3 libcurl3-dbg libcurl4-openssl-dev libreadline-dev \
                                 fuse fuse-dbg libfuse-dev libssl-dev libtool \
                                 libusb-1.0-0 libusb-1.0-0-dbg libusb-1.0-0-dev \
                                 libxml2 libxml2-dbg libxml2-dev libzip2 libzip-dev pkg-config
  PREREQ_SCRIPT

  $compsteps = <<-COMPILE_SCRIPT
    for pkg in libplist libusbmuxd libimobiledevice usbmuxd libirecovery libideviceactivation ideviceinstaller idevicerestore ifuse
      do
        rm -rf $pkg 2>&1 > /dev/null
        git clone --depth=1 https://github.com/libimobiledevice/$pkg.git && cd $pkg && sh autogen.sh --enable-debug 2>&1 | tee $pkg_autoconf.log
        make 2>&1 | tee $pkg_make.log
        sudo make install
        cd ..
    done
  COMPILE_SCRIPT

  $ldcacherebuild = "ldconfig"

  config.vm.provision "shell", inline: $prereqsteps
  config.vm.provision "shell", inline: $compsteps, privileged: false
  config.vm.provision "shell", inline: $ldcacherebuild
end
