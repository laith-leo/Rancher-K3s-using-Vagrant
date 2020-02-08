# -*- mode: ruby -*-
# vi: set ft=ruby :
# Editor: Laith (Leo) Alobaidy 

Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-18.04"
  config.vm.define "master", primary: true do |k3s|
    k3s.vm.hostname = "master"
    k3s.vm.network "private_network", ip: "192.168.100.100"
    k3s.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh", disabled: true
    k3s.vm.network "forwarded_port", guest: 22, host: 2022
    k3s.vm.network "forwarded_port", guest: 6443, host: 6443, host_ip: "0.0.0.0", auto_correct: true

      for i in 30000..30100
    k3s.vm.network "forwarded_port", guest: i, host: i, protocol: "tcp"
      end
    k3s.vm.provider "virtualbox" do |vb|
        vb.memory = "1024"
        vb.name = "master"
    end

#INSTALLING MASTER K3S, HELM, HELM STABLE CHART
  k3s.vm.provision "shell", inline: <<-SHELL
    curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--node-ip=192.168.100.100 --flannel-iface=eth1 --write-kubeconfig-mode 644 --kube-apiserver-arg="service-node-port-range=30000-30100" --no-deploy=servicelb --no-deploy=traefik" sh -
    sleep 5
    cp /var/lib/rancher/k3s/server/node-token /vagrant/
    sudo chmod 775 /vagrant/node-token
    sudo apt install bash-completion -y
    echo 'source <(kubectl completion bash)' >> /home/vagrant/.bashrc

    if [ "$HOSTNAME" = master ]; then
        curl -sfL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash && runuser -l vagrant -c 'helm repo add stable https://kubernetes-charts.storage.googleapis.com/'
    fi
    sudo chmod 775  /etc/rancher/k3s/k3s.yaml
    mkdir /home/vagrant/.kube &&  touch /home/vagrant/.kube/config && chown -R vagrant:vagrant /home/vagrant/.kube && sudo cat /etc/rancher/k3s/k3s.yaml >> /home/vagrant/.kube/config

    sudo echo "export KUBECONFIG=/home/vagrant/.kube/config" >> /home/vagrant/.profile
    echo "Installing Metalb"
    sleep 3
    kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.8.3/manifests/metallb.yaml

cat <<EOF > /tmp/metallb.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 192.168.100.101-192.168.100.102
EOF
      kubectl create -f /tmp/metallb.yaml
  SHELL
  end

#ADDING FIRST WORKER NODE

  config.vm.define "worker1" do |worker1|
    worker1.vm.hostname = "worker1"
    #worker1.vm.box = "bento/ubuntu-18.04"
    worker1.vm.network "private_network", ip: "192.168.100.101"
    worker1.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh", disabled: true
    worker1.vm.network "forwarded_port", guest: 22, host: 2023
    worker1.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
        vb.name = "worker1"
  end

  worker1.vm.provision "shell", inline: <<-SHELL
      curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--node-ip=192.168.100.101 --flannel-iface=eth1"  K3S_URL=https://192.168.100.100:6443 K3S_TOKEN=$(cat /vagrant/node-token) sh -  ; true
        SHELL
  end

#ADDING SECOND WORKER NODE

  config.vm.define "worker2" do |worker2|
    worker2.vm.hostname = "worker2"
    #worker2.vm.box = "bento/ubuntu-18.04"
    worker2.vm.network "private_network", ip: "192.168.100.102"
    worker2.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh", disabled: true
    worker2.vm.network "forwarded_port", guest: 22, host: 2024
    worker2.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
        vb.name = "worker2"
  end

  worker2.vm.provision "shell", inline: <<-SHELL
    curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--node-ip=192.168.100.102 --flannel-iface=eth1" K3S_URL=https://192.168.100.100:6443 K3S_TOKEN=$(cat /vagrant/node-token) sh -  ; true
      SHELL
  end
end
