# ####################################################################
# ################### CONFIGURATION VARIABLES ########################
# ####################################################################
IMAGE_NAME = "bento/ubuntu-18.04"   # Image to use
MEM = 2048                          # Amount of RAM
CPU = 2                             # Number of processors (Minimum value of 2 otherwise it will not work)
MASTER_NAME="master"                # Master node name
WORKER_NBR = 1                      # Number of workers node
NODE_NETWORK_BASE = "192.168.50"    # First three octets of the IP address that will be assign to all type of nodes
POD_NETWORK = "192.168.100.0/16"    # Private network for inter-pod communication



Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    # RAM and CPU config
    config.vm.provider "virtualbox" do |v|
        v.memory = MEM
        v.cpus = CPU
    end

    # Master node config
    config.vm.define MASTER_NAME do |master|
        
        # Hostname and network config
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "#{NODE_NETWORK_BASE}.10"
        master.vm.hostname = MASTER_NAME

        # Ansible role setting
        master.vm.provision "ansible" do |ansible|
            
            # Ansbile role that will be launched
            ansible.playbook = "roles/main.yml"

            # Groups in Ansible inventory
            ansible.groups = {
                "masters" => ["#{MASTER_NAME}"],
                "workers" => ["worker-[1:#{WORKER_NBR}]"]
            }

            # Overload Anqible variables
            ansible.extra_vars = {
                node_ip: "#{NODE_NETWORK_BASE}.10",
                node_name: "master",
                pod_network: "#{POD_NETWORK}"
            }
        end
    end

    # Worker node config
    (1..WORKER_NBR).each do |i|
        config.vm.define "worker-#{i}" do |worker|

            # Hostname and network config
            worker.vm.box = IMAGE_NAME
            worker.vm.network "private_network", ip: "#{NODE_NETWORK_BASE}.#{i + 10}"
            worker.vm.hostname = "worker-#{i}"

            # Ansible role setting
            worker.vm.provision "ansible" do |ansible|

                # Ansbile role that will be launched
                ansible.playbook = "roles/main.yml"

                # Groups in Ansible inventory
                ansible.groups = {
                    "masters" => ["#{MASTER_NAME}"],
                    "workers" => ["worker-[1:#{WORKER_NBR}]"]
                }

                # Overload Anqible variables
                ansible.extra_vars = {
                    node_ip: "#{NODE_NETWORK_BASE}.#{i + 10}"
                }
            end
        end
    end
end
