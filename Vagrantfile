# -*- mode: ruby -*-
# vi: set ft=ruby :
.
node1disk1 = "../tmp/node1.vmdk";
node2disk1 = "../tmp/node2.vmdk";
node3disk1 = "../tmp/node3.vmdk";
node4disk1 = "../tmp/node4.vmdk";
node5disk1 = "../tmp/node5.vmdk";


$s1 = <<-SCRIPT
sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/#g' /etc/ssh/sshd_config
systemctl restart sshd
rpm --import https://download.ceph.com/keys/release.asc
yum install epel-release -y
cp /vagrant/ceph.repo /etc/yum.repos.d/
yum install mc -y
yum install sshpass -y
yum install ceph -y
yum install cephadm -y
yum install podman -y
echo -e "\n192.168.50.11 node1\n192.168.50.12 node2\n192.168.50.13 node3\n192.168.50.14 node4\n192.168.50.15 node5" >> /etc/hosts
SCRIPT

$s2 = <<-SCRIPT
cephadm bootstrap --mon-ip 192.168.50.11 --ssh-user root
ssh -o "StrictHostKeyChecking no" node2
ssh -o "StrictHostKeyChecking no" node3
ssh -o "StrictHostKeyChecking no" node4
ssh -o "StrictHostKeyChecking no" node5

sshpass -p vagrant ssh-copy-id -f -i /etc/ceph/ceph.pub root@node2
sshpass -p vagrant ssh-copy-id -f -i /etc/ceph/ceph.pub root@node3
sshpass -p vagrant ssh-copy-id -f -i /etc/ceph/ceph.pub root@node4
sshpass -p vagrant ssh-copy-id -f -i /etc/ceph/ceph.pub root@node5
SCRIPT


$s3 = <<-SCRIPT
ceph orch host add node2
ceph orch host add node3
ceph orch host add node4
ceph orch host add node5
ceph orch apply mon "node1,node2,node3,node4,node5"
SCRIPT

$s4 = <<-SCRIPT
ceph orch device ls
ceph orch apply osd --all-available-devices
SCRIPT




Vagrant.configure("2") do |config|

#####

    config.vm.define "node2" do |server2|
        server2.vm.box = "centos/8"
        server2.vm.hostname = "node2"
        server2.vm.network "private_network", ip: "192.168.50.12"
        server2.vm.provider "virtualbox" do |vb|
            vb.memory = "4096"
            vb.cpus = 2
            if not File.exists?(node2disk1)
                vb.customize ['createhd', '--filename', node2disk1, '--variant', 'Fixed', '--size', 20 * 1024]
                vb.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 0, '--device', 1, '--type', 'hdd', '--medium', node2disk1]
            end
        server2.vm.provision "shell", inline: $s1
        end
    end

#####



    config.vm.define "node3" do |server3|
        server3.vm.box = "centos/8"
        server3.vm.hostname = "node3"
        server3.vm.network "private_network", ip: "192.168.50.13"
        server3.vm.provider "virtualbox" do |vb|
            vb.memory = "4096"
            vb.cpus = 2
            if not File.exists?(node3disk1)
                vb.customize ['createhd', '--filename', node3disk1, '--variant', 'Fixed', '--size', 20 * 1024]
                vb.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 0, '--device', 1, '--type', 'hdd', '--medium', node3disk1]
            end
        server3.vm.provision "shell", inline: $s1
        end
    end

#####

    config.vm.define "node4" do |server4|
        server4.vm.box = "centos/8"
        server4.vm.hostname = "node4"
        server4.vm.network "private_network", ip: "192.168.50.14"
        server4.vm.provider "virtualbox" do |vb|
            vb.memory = "4096"
            vb.cpus = 2
            if not File.exists?(node4disk1)
                vb.customize ['createhd', '--filename', node4disk1, '--variant', 'Fixed', '--size', 20 * 1024]
                vb.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 0, '--device', 1, '--type', 'hdd', '--medium', node4disk1]
            end
        server4.vm.provision "shell", inline: $s1
        end
    end

#####



    config.vm.define "node5" do |server5|
        server5.vm.box = "centos/8"
        server5.vm.hostname = "node5"
        server5.vm.network "private_network", ip: "192.168.50.15"
        server5.vm.provider "virtualbox" do |vb|
            vb.memory = "4096"
            vb.cpus = 2
            if not File.exists?(node5disk1)
                vb.customize ['createhd', '--filename', node5disk1, '--variant', 'Fixed', '--size', 20 * 1024]
                vb.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 0, '--device', 1, '--type', 'hdd', '--medium', node5disk1]
            end
        server5.vm.provision "shell", inline: $s1
        end
    end

#####

    config.vm.define "node1" do |server1|
        server1.vm.box = "centos/8"
        server1.vm.hostname = "node1"
        server1.vm.network "private_network", ip: "192.168.50.11"
        server1.vm.network "forwarded_port", guest: 8443, host: 8443
        server1.vm.provider "virtualbox" do |vb|
            vb.memory = "4096"
            vb.cpus = 2
        end
        server1.vm.provision "shell", inline: $s1
        server1.vm.provision "shell", inline: $s2
        server1.vm.provision "shell", inline: $s3
        server1.vm.provision "shell", inline: $s4
     end

end


