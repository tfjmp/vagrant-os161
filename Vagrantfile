VAGRANT_COMMAND = ARGV[0]

Vagrant.configure(2) do |config|

    if ENV['BOX_VERSION'] != nil
        config.vm.box = "ubuntu-16.04-" + ENV['BOX_VERSION']
    else
        config.vm.box = "ops-class/ubuntu-16.04"
        config.vm.box_version = ">= 0.0.2"
    end
    config.ssh.username = "trinity"
    config.vm.synced_folder "./guest", "/home/trinity/shared", create: true, owner: "trinity", group: "trinity"

    if Vagrant.has_plugin?("vagrant-timezone")
        config.timezone.value = :host
    end

    config.vm.provider "virtualbox" do |v|
        v.gui = true
        v.name = "OS-labs"
        v.cpus = "4"
        v.memory = "2048"
        # 24 Dec 2015 : GWA : Uncomment this if you want a GUI environment.
        # v.gui = true
        v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        v.customize ["guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 10000]
        v.customize ["modifyvm", :id, "--usb", "off"]
        v.customize ["modifyvm", :id, "--usbehci", "off"]
        v.customize ["modifyvm", :id, "--cableconnected1", "on"]
        v.customize ["modifyvm", :id, "--cpuexecutioncap", "70"]
    end

    config.trigger.after :destroy do |trigger|
        trigger.name = "Clean-up"
        trigger.info = "Cleaning things up!"
        trigger.ruby do |env,machine|
            require 'fileutils'
            FileUtils.rm_rf('guest')
        end
    end

    config.vm.provision "shell", inline: <<-SHELL
        apt-get -qq -o Dpkg::Use-Pty=0 update
        apt-get -qq -o Dpkg::Use-Pty=0 -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" upgrade
        add-apt-repository ppa:ops-class/os161-toolchain
        apt-get -qq -o Dpkg::Use-Pty=0 install os161-toolchain
        echo "Installation succeeded"
        echo "Now run vagrant ssh"
    SHELL
end

# vim: ts=2:sw=2:et:ft=ruby
