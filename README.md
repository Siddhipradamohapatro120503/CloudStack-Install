# Installation Of Cloudstack From Siddhiprada Mohapatro

 1. > sudo apt update

***The sudo apt update command is the most essential command in Linux to keep a system healthy and up-to-date. This command installs and downloads all the latest package information available for the packages currently installed on the system.***

***user is not in the sudoers file. this incident will be reported Type***
>  su 
>  apt install sudo 
>  user mod -aG sudo vboxuser-name 
>  restart 
> reboot



 ***IP address in Ubuntu:
Run command Ip a to check 
your machine Ip address = 192.168.122.132/24 and 
Your network interface = ens33  (means:-en-ethernet,s-slot no 33)
Run command ip r to check default gateway -192.168.122.2***

***Open  nano editor for 01-network-manager-all.yaml***

> `sudo nano /etc/netplan/01-network-manager-all.yaml`

 ***Password required : 
Then , Type script in nano editor*** 

    network:
     version: 2
     renderer: NetworkManager
     ethernets:
       ens33:
         dhcp4: no
         addresses: [your machine ip]
         gateway4: your gateway ip
         nameservers:
             addresses: [8.8.8.8,8.8.8.4]


***Press ctrl+s ——to save
Press ctrl+x——- to exit***


Apply Netplan

    sudo netplan apply


***-Netplan apply applies the current netplan configuration to a running system.
Using netplan gives a central location to describe simple-to-complex networking configurations that function from desktop to server and from cloud to IoT devices.
Restart NetworkManager***

***sudo systemctl restart NetworkManager***


***check and set host name***

    sudo nano /etc/hosts

***Give host name***

    192.168.122.132 	apache.c1.u1		cloud2003


**Set host name**

> sudo hostnamectl set-hostname cloud2003

***Check host name —-***
 `hostname —fqdn`                   (A fully qualified domain name (FQDN) is the complete domain name for a specific computer, or host, on the internet.)      

***Install bridge-utils—The bridge-utils package contains a utility needed to create and manage bridge devices. This is useful in setting up networks for a hosted virtual machine (VM).***


    sudo install bridge-utils -y
    sudo brctl addbr bridge0
    sudo brctl addif bridge0 ens33

    sudo nano /etc/netplan/01-network-manager-all.yaml

    network:
        version: 2
        renderer: networkd
        ethernets:
            ens33:
                dhcp4: no
                dhcp6: no
    
        bridges:
            cloudbr0:
                interfaces: [network interface]
                dhcp4: no
                dhcp6: no
                addresses: [your ip]
                gateway4: your gateway
                nameservers:
                    addresses: [8.8.8.8, 8.8.4.4]

    sudo apt install ntp 
    press y
    sudo systemctl enable ntp
    sudo systemctl start ntp 

 ***Install chrony***

    sudo apt install chrony

*chronyd is a daemon process that runs in the background. chronyd is used to adjust the system clock that runs in the kernel to synchronize with the NTP server. chronyd computes the rate at which the system clocks gains or loses time, and compensates the system clock at the rate.*

 **Install JDK**

    sudo apt install openjdk-11-jdk


**Install cloud stack**

    sudo nano /etc/apt/sources.list.d/cloudstack.list
    
    deb https://download.cloudstack.org/ubuntu focal 4.18
    
    wget -O - https://download.cloudstack.org/release.asc |sudo tee /etc/apt/trusted.gpg.d/cloudstack.asc
    
    sudo apt update
    
    sudo apt install cloudstack-management



    sudo apt install mysql-server
    
    sudo nano /etc/mysql/my.cnf

***

    [mysqld]
    server-id=1
    innodb_rollback_on_timeout=1
    innodb_lock_wait_timeout=600
    max_connections=350
    log-bin=mysql-bin
    binlog-format = ‘ROW’
***

    sudo systemctl start mysql
    
    sudo mysql_secure_installation
    
    sudo service mysql restart
    
    
    sudo systemctl status mysql.service
    
    
    
    sudo mysql

***Set up the database.***



    -- Create the cloud and cloud_usage databases
    CREATE DATABASE `cloud`;
    CREATE DATABASE `cloud_usage`;
    
    -- Create the cloud user
    CREATE USER cloud@`localhost` identified by '<Pwd@123>';
    CREATE USER cloud@`%` identified by '<Pwd@123>';
    
    -- Grant all privileges to the cloud user on the databases
    GRANT ALL ON cloud.* to cloud@`localhost`;
    GRANT ALL ON cloud.* to cloud@`%`;
    
    GRANT ALL ON cloud_usage.* to cloud@`localhost`;
    GRANT ALL ON cloud_usage.* to cloud@`%`;
    
    -- Grant process list privilege for all other databases
    GRANT process ON *.* TO cloud@`localhost`;
    GRANT process ON *.* TO cloud@`%`;
    
    
    \q ###### to quit
***

    sudo ufw allow mysql
***

    sudo cloudstack-setup-management
    
    sudo cloudstack-setup-databases cloud:Pawd@123@localhost --deploy-as=root;
    
    sudo mkdir -p /export/primary 
     sudo mkdir -p /export/secondary 
    sudo nano /etc/exports 
    
    echo "/export *(rw,async,no_root_squash,no_subtree_check)" >> /etc/exports


***Prepare NFS share***

    sudo apt install nfs-kernel-server

Export the /export directory.

    sudo exportfs -a
    
    service nfs-kernel-server restart
****

     sudo mkdir -p /mnt/primary /mnt/secondary

>      sudo echo "192.168.145.132:/export/primary /mnt/primary nfs rsize=8192,wsize=8192,timeo=14,intr,vers=3,noauto 0 2" >> /etc/fstab
>     bash: /etc/fstab: Permission denied
>     
>      sudo chmod 777 /etc/fstab



    sudo echo "Your_IP:/export/primary /mnt/primary nfs rsize=8192,wsize=8192,timeo=14,intr,vers=3,noauto 0 2" >> /etc/fstab
    
    
    sudo echo "Your_IP:/export/secondary /mnt/secondary nfs rsize=8192,wsize=8192,timeo=14,intr,vers=3,noauto 0 2" >> /etc/fstab
****

    sudo mount /mnt/primary
    
    sudo mount /mnt/secondary


Open the Browser and type the Url : http://(YourIP):8080/ 


### Refer to this video for more successfully Installation: [Click Hear](https://youtu.be/LP5yyXGdO5Y?si=XyE7jtegviGESFT9)
