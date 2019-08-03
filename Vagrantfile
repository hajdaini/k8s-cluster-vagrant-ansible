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

    # Configuration de la RAM et CPU
    config.vm.provider "virtualbox" do |v|
        v.memory = MEM
        v.cpus = CPU
    end

    # Création et configuration des noeuds de type master
    config.vm.define MASTER_NAME do |master|
        # Configuration de la VM
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "#{NODE_NETWORK_BASE}.10"
        master.vm.hostname = MASTER_NAME

        # Lancement + paramétrage du playbook Ansible
        master.vm.provision "ansible" do |ansible|
            # Rôle Ansible qui sera lancé
            ansible.playbook = "roles/main.yml"

            # Groupes d'hôtes de l'inventaire Ansible 
            ansible.groups = {
                "masters" => ["#{MASTER_NAME}"],
                "workers" => ["worker-[1:#{WORKER_NBR}]"]
            }

            # Surchargement des variables par défaut du playbook Ansible
            ansible.extra_vars = {
                node_ip: "#{NODE_NETWORK_BASE}.10",
                node_name: "master",
                pod_network: "#{POD_NETWORK}"
            }
        end
    end

    # Création et configuration des noeuds de type worker
    (1..WORKER_NBR).each do |i|
        config.vm.define "worker-#{i}" do |worker|

            # Configuration de la VM
            worker.vm.box = IMAGE_NAME
            worker.vm.network "private_network", ip: "#{NODE_NETWORK_BASE}.#{i + 10}"
            worker.vm.hostname = "worker-#{i}"

            # Lancement + paramétrage du playbook Ansible
            worker.vm.provision "ansible" do |ansible|

                # Rôle Ansible qui sera lancé
                ansible.playbook = "roles/main.yml"

                # Groupes d'hôtes de l'inventaire Ansible 
                ansible.groups = {
                    "masters" => ["#{MASTER_NAME}"],
                    "workers" => ["worker-[1:#{WORKER_NBR}]"]
                }

                # Surchargement des variables par défaut du playbook Ansible
                ansible.extra_vars = {
                    node_ip: "#{NODE_NETWORK_BASE}.#{i + 10}"
                }
            end
        end
    end
end   
