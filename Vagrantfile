Vagrant.configure("2") do |config|
  config.vm.box = "generic/alpine312"

  config.vm.network "private_network", type: "dhcp"

  # Disable the default share
  config.vm.synced_folder '.', '/vagrant', disabled: true

  config.vm.synced_folder ".kube/", "/kubeconfig", create:true

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "2048"
    vb.cpus = 4
    vb.customize ["modifyvm", :id, "--audio", "none"]
  end

  config.vm.provision "shell", inline: <<-SHELL
    set -e
    curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --no-deploy traefik" sh
    while [ ! -f /etc/rancher/k3s/k3s.yaml ]; do sleep 1; done
    sed  "s/127.0.0.1/$(ip route | grep eth1 | awk '/src/ {print $7}')/g" /etc/rancher/k3s/k3s.yaml > /kubeconfig/config
  SHELL
end
