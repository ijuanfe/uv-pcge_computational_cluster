# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile settings:

Vagrant.configure("2") do |config|

  # Configuracion servidor
  config.vm.define "server" do |server|
    server.vm.box = "debian/stretch64"
    server.vm.network "private_network", ip: "172.20.0.3"
    server.vm.hostname = "server"
    server.vm.provider :virtualbox do |vb|
        vb.customize [ 'modifyvm', :id, '--memory', '4096' ]
        vb.customize [ 'modifyvm', :id, '--cpus', '2' ]
        vb.customize [ 'modifyvm', :id, '--name', "cluster_server" ]
        vb.customize [ "modifyvm", :id, "--nic2", "hostonly" ]
    end
    
    # Para facilitar comunicacion entre los nodos del cluster
    server.vm.provision "Anadiendo IPs al archivo hosts", type: "shell", inline: <<-SCRIPT
      echo "172.20.0.3        server" >> /etc/hosts
      echo "172.20.0.4        node1"  >> /etc/hosts
      echo "172.20.0.5        node2"  >> /etc/hosts
    SCRIPT
    
    # Instalacion y configuracion del servidor NFS en Server
    server.vm.provision "Instalacion y configuracion de servidor NFS", type: "shell", inline: <<-SCRIPT
      sudo apt-get update
      sudo apt-get -y install nfs-kernel-server
      mkdir -p /export/nfs_shared_files
      mkdir /nfs_shared_files
      sudo chmod 777 /{export,nfs_shared_files} && sudo chmod 777 /export/*
      sudo mount --bind /nfs_shared_files /export/nfs_shared_files
      sudo echo "/export/nfs_shared_files *(rw,fsid=0,insecure,no_root_squash,no_subtree_check,async)" >> /etc/exports
      sudo /etc/init.d/nfs-kernel-server restart
    SCRIPT
    
    # Configuracion de llave publica del servidor para poder compartirla a sus nodos
    # para que el servidor pueda acceder a los nodos sin ingresar contrasena
    server.vm.provision "Configuracion de llave publica servidor", type: "shell", inline: <<-SCRIPT
      ssh-keygen -t rsa -b 2048 -f .ssh/id_rsa -q -N ""
      cp .ssh/id_rsa.pub /export/nfs_shared_files
      sudo chmod 777 .ssh/id_rsa*
      sudo apt -y install openmpi-bin libopenmpi-dev
    SCRIPT

    # Instalacion de OpenMPI
    server.vm.provision "Instalacion OpenMPI en servidor", type: "shell", inline: <<-SCRIPT
      sudo apt -y install openmpi-bin libopenmpi-dev
    SCRIPT

  end

  # Configuracion clientes

  (1..2).each do |i|

    config.vm.define "node#{i}" do |node|
      node.vm.box = "debian/stretch64"
      node.vm.hostname = "node#{i}"
      node.vm.network "private_network", ip: "172.20.0.#{i+3}"
      node.vm.provider :virtualbox do |vb|
          vb.customize [ 'modifyvm', :id, '--memory', '4096' ]
          vb.customize [ 'modifyvm', :id, '--cpus', '2' ]
          vb.customize [ 'modifyvm', :id, '--name', "cluster_node_#{i}" ]
          vb.customize [ "modifyvm", :id, "--nic2", "hostonly" ]
      end
      
      # Para facilitar comunicacion entre los nodos del cluster
      node.vm.provision "Anadiendo IPs al archivo hosts", type: "shell", inline: <<-SCRIPT
        echo "172.20.0.3        server" >> /etc/hosts
        echo "172.20.0.4        node1"  >> /etc/hosts
        echo "172.20.0.5        node2"  >> /etc/hosts
      SCRIPT

      # Instalacion y configuracion de cliente NFS
      node.vm.provision "Instalacion y configuracion de cliente NFS", type: "shell", inline: <<-SCRIPT
        sudo apt-get update
        sudo apt-get -y install nfs-common
        mkdir /nfs_shared_files
        mount 172.20.0.3:/export/nfs_shared_files /nfs_shared_files
        echo "172.20.0.3:/export/nfs_shared_files /nfs_shared_files nfs rsize=8192,wsize=8192,rw,user,owner,exec,auto 0 0" >> /etc/fstab
      SCRIPT

      # Configuracion de llave publica del servidor en directorio de cliente
      # para que el servidor pueda acceder a los nodos sin ingresar contrasena
      node.vm.provision "Configuracion de llave publica cliente", type: "shell", inline: <<-SCRIPT
        cat /nfs_shared_files/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys
      SCRIPT

      # Instalacion de OpenMPI
      node.vm.provision "Instalacion OpenMPI en cliente", type: "shell", inline: <<-SCRIPT
        sudo apt -y install openmpi-bin libopenmpi-dev
      SCRIPT

    end
  end
end
