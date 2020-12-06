PROJ_HOME = File.dirname(__FILE__)
PROJECT = File.basename(PROJ_HOME)
NODE_MEMORY = (ENV['NODE_MEMORY'] || 3072).to_i

Vagrant.configure("2") do |config|
  config.vm.box = "generic/alpine312"

  config.vm.network "private_network", type: "dhcp"

  # Disable the default share
  config.vm.synced_folder '.', '/vagrant', disabled: true

  config.vm.synced_folder ".kube/", "/kubeconfig", create:true

  config.vm.provider "virtualbox" do |vb|
    vb.name = "#{PROJECT}"
    vb.gui = false
    vb.memory = "#{NODE_MEMORY}"
    vb.cpus = 4
    vb.customize ["modifyvm", :id, "--audio", "none"]
    vb.customize ["modifyvm", :id, "--vram", "12"]
  end

  config.vm.define "#{PROJECT}-master" do |master|
    master.vm.hostname = "#{PROJECT}-master"

    master.vm.provider "virtualbox" do |vb|
      vb.name = "#{PROJECT}-master"
    end

    master.vm.provision "shell", inline: <<-SHELL
      set -e
      export IP_ADDRESS=$(ip route | grep eth1 | awk '/src/ {print $7}')
      curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --no-deploy traefik" sh
      while [ ! -f /etc/rancher/k3s/k3s.yaml ]; do sleep 1; done
      ip route | grep eth1 | awk '/src/ {print $7}' > /kubeconfig/master_ip
      chmod -R 644 /etc/rancher/k3s/k3s.yaml
      sed  "s/127.0.0.1/$(ip route | grep eth1 | awk '/src/ {print $7}')/g" /etc/rancher/k3s/k3s.yaml > /kubeconfig/config
      cp /var/lib/rancher/k3s/server/node-token /kubeconfig/node_token
    SHELL
  end
end
