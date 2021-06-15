Vagrant.configure(2) do |config|

    config.vm.box = "ubuntu/bionic64"
  
    config.vm.define "control-01" do |kube|
      kube.vm.hostname = "control-01"
      kube.vm.network "private_network", ip: "192.168.50.101"
      config.vm.provider :virtualbox do |vb|
         vb.customize ["modifyvm", :id, "--memory", 2048]
         vb.customize ["modifyvm", :id, "--cpus", 3]
      end  
      kube.vm.provision "shell", inline: $script
    end
   
   config.vm.define "worker-01" do |kube|
      kube.vm.hostname = "worker-01"
      kube.vm.network "private_network", ip: "192.168.50.102"
      config.vm.provider :virtualbox do |vb|
         vb.customize ["modifyvm", :id, "--memory", 2048]
         vb.customize ["modifyvm", :id, "--cpus", 1]
      end
      kube.vm.provision "shell", inline: $script
    end
  
   config.vm.define "worker-02" do |kube|
      kube.vm.hostname = "worker-02"
      kube.vm.network "private_network", ip: "192.168.50.103"
      config.vm.provider :virtualbox do |vb|
         vb.customize ["modifyvm", :id, "--memory", 2048]
         vb.customize ["modifyvm", :id, "--cpus", 1]
      end
      kube.vm.provision "shell", inline: $script
    end

    $script = <<SCRIPT
sudo echo "192.168.50.101 control-01" >> /etc/hosts
sudo  echo "192.168.50.102 worker-01" >> /etc/hosts
sudo  echo "192.168.50.103 worker-02" >> /etc/hosts
echo starting..
sudo cat >> /etc/ufw/sysctl.conf <<EOF
net/bridge/bridge-nf-call-ip6tables = 1
net/bridge/bridge-nf-call-iptables = 1
net/bridge/bridge-nf-call-arptables = 1
EOF

export DEBIAN_FRONTEND=noninteractive
sudo apt-get -qq install ebtables ethtool
sudo apt-get -qq update
sudo apt-get -qq install -y docker.io apt-transport-https curl
sudo mkdir /etc/docker
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
sudo systemctl enable docker
sudo systemctl daemon-reload
sudo systemctl restart docker
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
sudo cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get -qq update
sudo apt-get -qq install -y kubelet kubeadm kubectl kubernetes-cni
sudo apt-mark hold kubelet kubectl kubeadm
sudo rm -rf /var/lib/kubelet/*
echo I am turning off swap...
sudo swapoff -a
echo  joining NFS...
sudo apt-get install nfs-common -y
# Set external DNS
sudo sed -i -e 's/#DNS=/DNS=8.8.8.8/' /etc/systemd/resolved.conf
sudo service systemd-resolved restart



SCRIPT

end