# kuberntes-using-vagrant-and-kubeadm
installing kuberntes 3 nodes one master (control plane and 2 worker )

first run :  vagrant up

second : vagrant ssh control-01
now inside the control node  init the kubeadm :    sudo kubeadm init --apiserver-advertise-address=192.168.50.101 --pod-network-cidr=192.168.0.0/16

this should botstrab your cluster please fellow the video : https://youtu.be/oEwrpIHjgDA
