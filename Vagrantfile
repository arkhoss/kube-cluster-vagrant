IMAGE_NAME = "ubuntu/xenial64"

N = 3

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
        v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
        v.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
    end

    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.box_version = "20210209.0.0"
        master.vm.network "private_network", ip: "192.168.60.10"
        master.vm.hostname = "k8s-master"
        master.vm.synced_folder "./", "/mnt"
        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "ansible/playbooks/k8s-master.yml"
            ansible.extra_vars = {
                node_ip: "192.168.60.10",
            }
        end
    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.box_version = "20210209.0.0"
            node.vm.network "private_network", ip: "192.168.60.#{i + 13}"
            node.vm.hostname = "node-#{i}"
            node.vm.synced_folder "./", "/mnt"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "ansible/playbooks/k8s-nodes.yml"
                ansible.extra_vars = {
                    node_ip: "192.168.60.#{i + 13}",
                }
            end
        end
    end
end
