# Install

## Vagrant Install

## Vagrantfile

## VM Create using vagrant

### Deploy node create

- vagrant file create

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.define "deploy" do |deploy|
      deploy.vm.box = "ubuntu/jammy64"
      deploy.vm.disk :disk, size: "100GB", primary: true
      deploy.vm.provider "virtualbox" do |vb|
        vb.name = "deploy"
        vb.cpus = 1
        vb.memory = 3072
      end
      deploy.vm.host_name = "deploy"
      deploy.vm.network "private_network", ip: "192.168.100.11"
      deploy.vm.provision "shell", path:
      "bootstrab.sh"
  end
end
```

- bootstrab file create
```bash
echo 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC9nrbl4g4gv659wKWKE/xqsN8Wv0puHW9W3hFUT+CjYmJuNA6DfYw3fVmiebJF1RtwJ5PuOTAqOcXolg8SOPnTo1dWtqChCPv4R97faFXVB2dzmzhfrFBGNCVrAAQiCidM2Cc7eLlh4BMaNkcnhVg+NYv6d1/+QwOPHALJJFhcqDoMC+f7Jmi/mazNFIZm9/yrvC+qEB1XwKutrARbBmCXnlnXxEveqo4b/esAO0roT9hiBrvt9FKmUDPzc7prP8hxlsqCdbqbFcrKbYAAB0J7Qci6hU011kIYXarmgJAJk+wrhobllzKcqjQfzfxw85ykfFRDhekKJbFR0dvxPJNf dding@DESKTOP-JU7P9KM' >> /home/vagrant/.ssh/authorized_keys

echo 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC9nrbl4g4gv659wKWKE/xqsN8Wv0puHW9W3hFUT+CjYmJuNA6DfYw3fVmiebJF1RtwJ5PuOTAqOcXolg8SOPnTo1dWtqChCPv4R97faFXVB2dzmzhfrFBGNCVrAAQiCidM2Cc7eLlh4BMaNkcnhVg+NYv6d1/+QwOPHALJJFhcqDoMC+f7Jmi/mazNFIZm9/yrvC+qEB1XwKutrARbBmCXnlnXxEveqo4b/esAO0roT9hiBrvt9FKmUDPzc7prP8hxlsqCdbqbFcrKbYAAB0J7Qci6hU011kIYXarmgJAJk+wrhobllzKcqjQfzfxw85ykfFRDhekKJbFR0dvxPJNf dding@DESKTOP-JU7P9KM' >> /root/.ssh/authorized_keys

# setup the sshd_config
sudo sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
sudo service ssh reload

# Timezone setup
sudo timedatectl set-timezone Asia/Jakarta

# apt-update
apt update
apt upgrade -y
apt install net-tools -y
apt install dstat -y
apt install chrony -y
apt autoremove -y

# chrony 
sed -i '24a\allow 192.168.100.0/24' /etc/chrony/chrony.conf

# chrony service restart
systemctl restart chrony.service


# disable service
systemctl disable apparmor.service
systemctl disable ufw.service
systemctl stop apparmor.service
systemctl stop ufw.service
swapoff -a

# /etc/hosts add
cat <<EOF >> /etc/hosts

192.168.100.11  deploy
192.168.100.31  mas1
192.168.100.32  mas2
192.168.100.33  mas3
192.168.100.41  wrk1
192.168.100.42  wrk2
EOF


# bash color
echo "export PS1='\033[01;31m[\u\[\033[01;32m\]@\h \[\033[01;34m\w]\[\033[00m\]\$ '" >> /root/.bashrc
```
### Kubernetes node create

- bootstrab file create
```bash
# ssh-key copy
echo 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCC+6kWmfnf5BGJ9zRywET5zyR5QTIsqUUYJIM0XsHtOBSxnzvIPrPdZ+sTuuiBrEnegOuti6sW908/LD0AblHw6szNpciLEcFBV+cBnTpU6CANAEyzBaJTIuVUARSa9XMpOpLAwQpvYBRvflszUtfx1OxkX78aNn0oReuh/xj0ZNo44lL6bQHeHTBEftmIrwGIH9Wbva8Bh3h9kxCPo2yYsig/TT7Mbz50jPCs74URN1YjuOoo90BySqf2MQlLyM9Mvpxu8ux98neOgV77nsLCSHK22RA8qiNjbuyy6SFgyozDdaFaJ2xGH2gbmj7sqCoTHgd6WJJmrnhquaLTPuAobZ5oOudfQWvKv3LQoW38mDBGSEEy7/NgMGoTHJzvfzyT5D6YrYXDfp9loLyNGp6MmZ2oOYVB6caHp4158E6krNTPANrMK9yWx8V09J2kbyVB1QgI+m4widwPDmCgjlZfiqIbn/cXdpmDkE51QN/ExV7b+PacrIyIK1IoZbWtF2M= root@deploy' >>  /root/.ssh/authorized_keys

# setup the sshd_config
sudo sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
sudo service ssh reload

# Timezone setup
sudo timedatectl set-timezone Asia/Jakarta

# apt-update
apt update
#apt upgrade -y
apt install net-tools -y
apt install chrony -y
apt install dstat -y
apt autoremove -y

# Chrony server ip change

sed -i 's/^pool/# pool/' /etc/chrony/chrony.conf
sed -i '24a\server 192.168.100.11' /etc/chrony/chrony.conf

# disable service
systemctl disable apparmor.service
systemctl disable ufw.service
systemctl stop apparmor.service
systemctl stop ufw.service
swapoff -a

# /etc/hosts add
cat <<EOF >> /etc/hosts

192.168.100.11  deploy
192.168.100.21  m1
192.168.100.22  m2
192.168.100.23  m3
192.168.100.31  w1
192.168.100.32  w2
192.168.100.100 db
EOF


# ETCD path copy

hostname=$(hostname)

if [[ $hostname == m* ]]; then
cat <<EOF >> /root/.bashrc
export ETCDCTL_DIAL_TIMEOUT=3s
export ETCDCTL_CACERT=/etc/ssl/etcd/ssl/ca.pem
export ETCDCTL_CERT=/etc/ssl/etcd/ssl/node-$hostname.pem         # mas1은 각 노드 hostname으로 변경
export ETCDCTL_KEY=/etc/ssl/etcd/ssl/node-$hostname-key.pem      # mas1은 각 노드 hostname으로 변경
EOF

else
    echo "It is not master node"
fi

# bash color

echo "export PS1='\033[01;31m[\u\[\033[01;32m\]@\h \[\033[01;34m\w]\[\033[00m\]\$ '" >> /root/.bashrc

source ~/.bashrc
```

- vagrant file create

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  ### Master Node ####
 (1..1).each do |i|
    config.vm.define "m#{i}" do |mas|
      mas.vm.box = "ubuntu/jammy64"
      mas.vm.provider "virtualbox" do |vb|
        vb.name = "m#{i}"
        vb.cpus = 3
        vb.memory = 3072
        vb.customize ["modifyvm", :id, "--groups", "/k8s-cluster"]
      end
      mas.vm.host_name = "m#{i}"
      mas.vm.network "private_network", ip: "192.168.100.2#{i}"
      mas.vm.provision "shell", path:
      "bootstrab.sh"
    end
 end

  ### Worker Node ###

  (1..3).each do |i|
    config.vm.define "w#{i}" do |work|
      work.vm.box = "ubuntu/jammy64"
      work.vm.provider "virtualbox" do |vb|
        vb.name = "w#{i}"
        vb.cpus = 3
        vb.memory = 3072
        vb.customize ["modifyvm", :id, "--groups", "/k8s-cluster"]
      end
      work.vm.host_name = "w#{i}"
      work.vm.network "private_network", ip: "192.168.100.3#{i}"
      work.vm.provision "shell", path:
      "bootstrab.sh"
    end
   end
end
```
