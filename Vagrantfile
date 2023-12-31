Vagrant.configure("2") do |config|

    config.vm.box = "ubuntu/jammy64"
  
    config.vm.provision "shell", inline: <<-SHELL
      ufw disable
      apt-get -y install net-tools
    SHELL
  
    config.vm.define "jannabelS"
    config.vm.hostname = "jannabelS"
    config.vm.network "private_network", ip: "192.168.56.110"
    config.vm.provision "file", source: "confs/deployment.yaml", destination: "/tmp/deployment.yaml"
    config.vm.provision "file", source: "confs/app1", destination: "/tmp/app1"
    config.vm.provision "file", source: "confs/app2", destination: "/tmp/app2"
    config.vm.provision "file", source: "confs/app3", destination: "/tmp/app3"
    config.vm.provider "virtualbox" do |v|
      v.name = "jannabelS"
      v.memory = 2048
      v.cpus = 2
    end
    
    config.vm.provision "shell", inline: <<-SHELL
      echo "127.0.1.1 $(hostname)" >> /etc/hosts
      sudo ip link add eth1 type dummy
      sudo ip addr add 192.168.56.110/24 dev eth1
      sudo ip link set eth1 up
      echo "Installing k3s v1.21.4+k3s1..."
      export INSTALL_K3S_VERSION=v1.21.4+k3s1
      export INSTALL_K3S_EXEC="server --write-kubeconfig-mode 644 --node-ip=192.168.56.110"
      curl -sfL https://get.k3s.io | sh -
  
      echo "Setting up aliases"
      echo "alias k='kubectl'" >> /home/vagrant/.bashrc
      echo "alias c='clear'" >> /home/vagrant/.bashrc
  
      mv /tmp/deployment.yaml /home/vagrant
      mv /tmp/app1 /home/vagrant
      mv /tmp/app2 /home/vagrant
      mv /tmp/app3 /home/vagrant
  
      /usr/local/bin/kubectl create configmap app-one-html --from-file /home/vagrant/app1/index.html --namespace=kube-system
      /usr/local/bin/kubectl create configmap app-two-html --from-file /home/vagrant/app2/index.html --namespace=kube-system
      /usr/local/bin/kubectl create configmap app-three-html --from-file /home/vagrant/app3/index.html --namespace=kube-system
  
      /usr/local/bin/kubectl apply -f /home/vagrant/deployment.yaml
    SHELL
  
  end