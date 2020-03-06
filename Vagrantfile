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
  config.vm.box = "bento/ubuntu-18.04"

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
   config.ssh.insert_key = false
   config.vm.network "private_network", ip: "192.168.1.32"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
  # フォルダのマウント ホスト側になかったらフォルダ作成する。
  config.vm.synced_folder "./vagrant_files", "/home/vagrant/", :mount_options => ["dmode=777", "fmode=777"],create:"true"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  # GUI設定
   config.vm.provider "virtualbox" do |vb|
     # Display the VirtualBox GUI when booting the machine
     vb.gui = true
  
     # Customize the amount of memory on the VM:
     vb.memory = "4096" # メモリ4GB(とりあえず)
     vb.customize [
     "modifyvm", :id,
     "--vram", "256", # ビデオメモリ確保（フルスクリーンモードにするため）
     "--clipboard", "bidirectional", # クリップボードの共有
     "--draganddrop", "bidirectional", # ドラッグアンドドロップ可能に
     "--cpus", "2", # CPUは2つ
     "--ioapic", "on" # I/O APICを有効化
   ]
   end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision :shell, :inline =>  <<-SHELL
    # 日本語化 https://www.ubuntulinux.jp/japanese のための準備
    wget -q https://www.ubuntulinux.jp/ubuntu-ja-archive-keyring.gpg -O- | sudo apt-key add -
    wget -q https://www.ubuntulinux.jp/ubuntu-jp-ppa-keyring.gpg -O- | sudo apt-key add -
    sudo wget https://www.ubuntulinux.jp/sources.list.d/bionic.list -O /etc/apt/sources.list.d/ubuntu-ja.list
    sudo apt update -y
    sudo apt-get upgrade -y
    sudo apt-get install ubuntu-desktop ubuntu-defaults-ja -y
    # タイムゾーンを日本時間に変更
    sudo timedatectl set-timezone Asia/Tokyo
    # デフォルトのエディタを nano から vim に変更
    sudo update-alternatives --set editor $(update-alternatives --list editor | grep 'vim.basic')
    sudo apt update -y
    sudo apt-get upgrade -y
    SHELL
end
