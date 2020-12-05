Vagrant.configure("2") do |config|
  config.vm.box = "bento/debian-10"

  config.vm.network "private_network", type: "dhcp"

  # Disable the default share
  config.vm.synced_folder '.', '/vagrant', disabled: true

  config.vm.synced_folder ".kube/", "/kubeconfig", create:true

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "2048"
    vb.cpus = 4
  end

  config.vm.provision "shell", inline: <<-SHELL
    set -e
    swapoff -a
    curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --no-deploy traefik" sh
    chmod -R 755 /etc/rancher/k3s/k3s.yaml
    sed  "s/127.0.0.1/$(/sbin/ifconfig eth1 | awk '/inet / {print $2}')/g" /etc/rancher/k3s/k3s.yaml > /kubeconfig/config
  SHELL
end
