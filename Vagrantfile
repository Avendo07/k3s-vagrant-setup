Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2004" # Ubuntu 22.04
#   config.vm.synced_folder ".", "/vagrant", type: "nfs", mount_options: ["vers=3,tcp"]

  # Common configurations for all machines
  config.vm.provision "shell", inline: <<-SHELL
    # Fix DNS resolution
    echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf > /dev/null
    echo "nameserver 8.8.4.4" | sudo tee -a /etc/resolv.conf > /dev/null

    echo "192.168.56.101 k3s-master" | sudo tee -a /etc/hosts
    echo "192.168.56.102 k3s-worker1" | sudo tee -a /etc/hosts
    echo "192.168.56.103 k3s-worker2" | sudo tee -a /etc/hosts
  SHELL

  # Master node
  config.vm.define "k3s-master" do |master|
    master.vm.hostname = "k3s-master"
    master.vm.network "private_network", ip: "192.168.56.101"
    master.vm.provider :libvirt do |lv|
      lv.memory = 1024
      lv.cpus = 1
    end
    master.vm.provision "shell", inline: <<-SHELL
      curl -sfL https://get.k3s.io | sh -

      mkdir -p $HOME/.kube
      sudo cp /etc/rancher/k3s/k3s.yaml $HOME/.kube/config
    SHELL
  end

  # Worker node 1
  config.vm.define "k3s-worker1" do |worker1|
    worker1.vm.hostname = "k3s-worker1"
    worker1.vm.network "private_network", ip: "192.168.56.102"
    worker1.vm.provider :libvirt do |lv|
      lv.memory = 2048
      lv.cpus = 4
    end
  end

  # Worker node 2
  config.vm.define "k3s-worker2" do |worker2|
    worker2.vm.hostname = "k3s-worker2"
    worker2.vm.network "private_network", ip: "192.168.56.103"
    worker2.vm.provider :libvirt do |lv|
      lv.memory = 4096
      lv.cpus = 2
    end
  end

  config.vm.provision "ansible" do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.groups = {
      "k3s_master"  => ["k3s-master"],
      "k3s_workers" => ["k3s-worker1", "k3s-worker2"]
    }
    ansible.playbook = "playbooks/playbook-k3s-cluster.yaml" # Use the combined playbook
    # Remove ansible.limit here, as the playbook itself will handle targeting based on 'hosts:'
  end
end