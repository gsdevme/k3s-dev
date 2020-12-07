PROJ_HOME = File.dirname(__FILE__)
PROJECT = File.basename(PROJ_HOME)
NODE_MEMORY = (ENV['NODE_MEMORY'] || 3072).to_i

Vagrant.configure("2") do |config|
  config.vm.box = "generic/alpine312"

  config.vm.network "private_network", type: "dhcp"

  # Disable the default share
  config.vm.synced_folder '.', '/vagrant', disabled: true

  config.vm.synced_folder ".kube/", "/kubeconfig", create:true

  # Use rsync for libvirt
  config.vm.provider :libvirt do |libvirt, override|
    override.vm.synced_folder ".kube/", "/kubeconfig", create:true, type: 'rsync'
  end

  config.vm.provider "virtualbox" do |vb|
    vb.name = "#{PROJECT}"
    vb.gui = false
    vb.memory = "#{NODE_MEMORY}"
    vb.cpus = 4
    vb.customize ["modifyvm", :id, "--audio", "none"]
    vb.customize ["modifyvm", :id, "--vram", "12"]
  end
end
