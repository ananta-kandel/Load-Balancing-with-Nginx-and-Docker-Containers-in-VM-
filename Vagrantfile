Vagrant.configure("2") do |config|

    config.vm.box = "bandit145/centos_stream9_arm"
    config.vm.network "private_network", ip: "192.168.56.12"
    config.vm.provider "vmware_desktop" do |vmware|
    vmware.gui = true
    config.vm.network "public_network", ip: "192.168.1.10"
    vmware.allowlist_verified = true
    end
    end