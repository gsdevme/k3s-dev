PROJ_HOME = File.dirname(__FILE__)
PROJECT = File.basename(PROJ_HOME)
NODE_MEMORY = (ENV['NODE_MEMORY'] || 1024).to_i
NUM_NODES = (ENV['NUM_NODES'] || 0).to_i

if not Dir.exist?('.kube/')
  raise(".kube/ folder does not exist, please create it e.g. 'mkdir .kube'")
end

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
      echo $IP_ADDRESS > /kubeconfig/master_ip
      curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --write-kubeconfig-mode '0664' --no-deploy traefik --node-external-ip '$IP_ADDRESS' --tls-san '$IP_ADDRESS'" sh
      while [ ! -f /etc/rancher/k3s/k3s.yaml ]; do sleep 1; done
      sed  "s/127.0.0.1/$(ip route | grep eth1 | awk '/src/ {print $7}')/g" /etc/rancher/k3s/k3s.yaml > /kubeconfig/config
      cp /var/lib/rancher/k3s/server/node-token /kubeconfig/node_token
      echo "K3s cluster ready at $IP_ADDRESS"
    SHELL
  end

  (1..NUM_NODES).each do |i|
    config.vm.define "#{PROJECT}-node-#{i}" do |node|
      node.vm.hostname = "#{PROJECT}-node-#{i}"

      node.vm.provider "virtualbox" do |vb|
        vb.name = "#{PROJECT}-node-#{i}"
        vb.memory = "#{NODE_MEMORY}"
        vb.cpus = 2
      end

      node.vm.provision "shell", inline: <<-SHELL
        set -e
        export MASTER_IP=$(cat /kubeconfig/master_ip)
        export NODE_TOKEN=$(cat /kubeconfig/node_token)
        curl -sfL https://get.k3s.io | K3S_URL=https://$MASTER_IP:6443/ K3S_TOKEN=$NODE_TOKEN sh
      SHELL
    end
  end
end
