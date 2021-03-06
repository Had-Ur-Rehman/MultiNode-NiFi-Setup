# Setting Up Linux For NiFi
This is a guide to setup multi-node NiFi Cluster in CentOS7

## 1. Downloading Required Stuff:

* CentOS7ISO.
* VirtualBox.

## 2. Creating a VM Using VirtualBox:

* Click on the new button.
* Add the name of the VM as per your choice. 1. Type: Linux 2. Version: Red-Hat (64 bit)
* Assign memory according to the current Memory installed on your system. (4GB Recommended)
* Select Create a virtual hard disk now option
* Select VDI (Virtualbox Disk Image)
* Select Dynamically Allocated
* Assign the storage according to the available storage on your system. (Atleast 8GB)
* Creating Adapters 1. Goto File > Host Network Manager 2. There are 3 buttons shown namely: create, remove and properties 3. Create 2 Adapters and name them virtualbox host ethernet adapter and virtualbox host ethernet adapter1
* Now go back to the VirtualBox and select the VM that is just created and click on the settings button.
* Goto Adapter tab. 1. Under Adapter2 tab, select the Enable Network Adapter checkbox and assign virtualbox host ethernet adapter. 2. Goto Adapter3 tab, select the Enable Network Adapter checkbox and assign virtualbox host ethernet adapter1 .
* Now goto Storage tab in settings and Select the empty disk. on the right side, add the CentOS7 ISO Image.
* Now run the VM

## 3. Installation of CentOS7 on VM

* Select the language
* Add date and time
* Goto Network and Hostname and select all 3 adapters
* Click on Begin Installation
* add root password

## 4. Setup Static IP Address:

* To set a static IP address, use the following command to add configurations:
```
vi /etc/sysconfig/network-scripts/ifcfg-enp0s8

```        
* File will open with the following contents:
```
 TYPE=Ethernet
 BOOTPROTO=dhcp
 DEFROUTE=yes
 PEERDNS=yes
 PEERROUTES=yes
 IPV4_FAILURE_FATAL=no
 IPV6INIT=yes
 IPV6_AUTOCONF=yes
 IPV6_DEFROUTE=yes
 IPV6_PEERDNS=yes
 IPV6_PEERROUTES=yes
 IPV6_FAILURE_FATAL=no
 NAME=enp0s8
 UUID=c3fb64f0-6ff5-4294-9354-8e1279971d7e
 DEVICE=enp0s8
 ONBOOT=no

```

* change the following 
```
 BOOTPROTO=static
 ONBOOT=yes
 IPADDR=192.168.XXX.XXX
 NETMASK=255.255.255.0

```

* restart the network by executing the command:
```
systemctl restart network
```

## Yum Update
  Update yum using the following command:
  ```
   yum update
  ```
  
## Check memory and storage
To check memory and storage of linux write the following commands respectively:
```
free -m
df -h
```

## 5.Maximum number of open file requirements
* To check the current number of available number of open file descriptors, use the following command:
```
  ulimit -Sn
  ulimit -Hn
```
* To set the value of open file descriptors. (recommended: 10000)
```
 ulimit -n 10000
```

## 6. Changing Hostname of machine
* By Default, the hostname is: ```local host```
* To check the current running hostname, run the following command:
```
hostname -f
```
* To check the details of hostname, run the command:
```
hostnamectl
```
* To change the hostname of the system:
```
 hostnamectl set-hostname hostname
```

* reboot the system using ```reboot``` command

## 7.Setting the hostname in the hostfile

* To view the hosts in the hosts file, enter the following command:
```
vi /etc/hosts
```
* add the hosts in a fashion of adding an IP of a node and the hostname, the sample is given below:
```
 127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
 ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
 192.168.XXX.XXX master.company.com master
 192.168.YYY.XXX datanode1.company.com datanode1 
 192.168.XXX.YYY datanode2.company.com datanode2
```
* master, datanode1 and datanode2 are name of machines
* Repeat the same step and add this in other machines also

## 9. Setup NTP on the server and the browser host
NTP ensures that the clocks of the nodes in the cluster are synchronized. follow the steps given below in order to set the NTP on each node:
* Install NTP using the command:
```
 yum install -y ntp
```

* Enable NTP using the command:
```
 systemctl enable ntpd
```

* Following commands are used to start and restart the NTP service.
```
 systemctl start ntpd
 systemctl restart ntpd
```

* If you want to check the sync status, use the following command:

```
 ntpstat
```

## 10. Disabling Firewalls

write the following commands in linux to disable firewall:

```
 systemctl disable firewalld
 service firewalld stop
```

## 11. Set the Network Config File
* Change the contents of the following file by using the command:
 ```
  vi /etc/sysconfig/network
 ```
* make the following changes in the file:
```
 NETWORKING=yes
 HOSTNAME=localhost
```

## 12. Disable SELinux and PackageKit and check umask value
It is important to disable SELinux.
* Use the following command to disable SELinux:
```
setenforce 0
```

* to permenantly disable SELinux, make sure to set ```SELINUX=disabled``` in the following file to make sure that it is not enables after the system is rebooted:

```
vi /etc/selinux/config
```

* To check the current umask, run the following command:
```
 umask
 ```
 
* set the umask just fot the current login session:
```
umask 0022
```

* permenantly changing the umask for all the users:
```
 echo umask 0022 >> /etc/profile
```

# NiFi Installation on a single Node:
* create new non-root user named nifi
```
cd /home/nifi

```

* then to download nifi file write following command: 

```
wget -O nifi-1.12.1-bin.tar.gz https://downloads.apache.org/nifi/1.12.1/nifi-1.12.1-bin.tar.gz
```

* Extract the file and go to extracted file:

```
tar -xvf nifi-1.12.1-bin.tar.gz
cd nifi-1.12.1
cd bin

```

* By default nifi will have port 8080 if you want to change the port then follow the steps
```
cd /home/nifi/nifi-1.12.1/conf
ls
vi nifi.properties


```
search for port or 8080 and change it to whatever you want to

* Checking Services of Nifi
go to bin folder of nifi-1.12.1 and execute the following command and will give you service for nifi
``` 
./nifi.sh –help 
```

* Starting NiFi and checking status

```
./nifi.sh start
./nifi.sh status
```

**Got Error During startup of NiFi solution was to install Java**

## Installing Java
Install Java 8 or 11 because nifi only supports these Java

also install it in 	```cd /home/nifi```

* Add the following command to download java in linux ( I specifically download Java 8 ) 

```
wget -c --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.tar.gz -O jdk-8-linux-x64.tar.gz
```
* Then extract the downloaded file

```
tar -xvf jdk-8-linux-x64.tar.gz
```

* Finding Java
```
cd /home/nifi/jdk1.8.0_131
cd bin
```

## Setting Path of Java

* Go to bash and add the following command:

```
export JAVA_HOME=/home/nifi/jdk1.8.0_131
```
To check if Java is working write ``` java ``` in linux shell.

## Starting NiFi:

* Write the following command to start Nifi:

```
./nifi.sh start 
```

* Go to nifi folder then go to logs
then write following command:

```
tail -f nifi-app.log
```

if its running properly it will show ip of machine with the port that you have set.
Enter the Ip address of machine with port in windows browser
For Example: ``` 192.168.XXX.XXX:8080 ```


# 3 Node Cluster of NiFi
I deploy the binaries file on my three instances and unzip it. I now have a NiFi directory on each one of my nodes.

The first thing is to configure the list of the ZK (ZooKeeper) instances in the configuration file ```./conf/zookeeper.properties```

* Since our three NiFi instances will run the embedded ZK instance, I just have to complete the file with the following properties:
```
server.1=master:2888:3888;2181
server.2=datanode1:2888:3888;2181
server.3=datanode2:2888:3888;2181

```
Then, everything happens in the ```./conf/nifi.properties```.

Go to nifi.properties.

* First, I specify that NiFi must run an embedded ZK instance, with the following property:
```
nifi.state.management.embedded.zookeeper.start=true
```

* I also specify the ZK connect string:

```
master:2181,datanode1:2181,datanode2:2181
```

As you can notice, the ./conf/zookeeper.properties file has a property named dataDir. 
By default, this value is set to ./state/zookeeper. 
If more than one NiFi node is running an embedded ZK, it is important to tell the server which one it is.

To do that, you need to create a file name myid and placing it in ZK’s data directory. The content of this file should be the index of the server as previously specify by the server. property.

* For master:
```
mkdir ./state                   
	mkdir ./state/zookeeper
	echo 1 > ./state/zookeeper/myid   

```
(myid is server.myid for example in our case it is server.1)
myid will contain value 1

* For datanode1:
```
mkdir ./state
mkdir ./state/zookeeper
echo 2 > ./state/zookeeper/myid
```

* For datanode2:

```
mkdir ./state
mkdir ./state/zookeeper
echo 3 > ./state/zookeeper/myid

```
* Then we go to clustering properties. For this article, we are setting up an unsecured cluster, so we must keep:

```
nifi.cluster.protocol.is.secure=false
```
Then, we have the following properties:

* For master

```

    nifi.cluster.is.node=true
    nifi.cluster.node.address=master
    nifi.cluster.node.protocol.port=9999
    nifi.cluster.node.protocol.threads=10
    nifi.cluster.node.event.history.size=25
    nifi.cluster.node.connection.timeout=5 sec
    nifi.cluster.node.read.timeout=5 sec
    nifi.cluster.firewall.file=

```

* For datanode1

```
    nifi.cluster.is.node=true
    nifi.cluster.node.address=datanode1
    nifi.cluster.node.protocol.port=9999
    nifi.cluster.node.protocol.threads=10
    nifi.cluster.node.event.history.size=25
    nifi.cluster.node.connection.timeout=5 sec
    nifi.cluster.node.read.timeout=5 sec
    nifi.cluster.firewall.file=

```

* For datanode2

```
    nifi.cluster.is.node=true
    nifi.cluster.node.address=datanode2
    nifi.cluster.node.protocol.port=9999
    nifi.cluster.node.protocol.threads=10
    nifi.cluster.node.event.history.size=25
    nifi.cluster.node.connection.timeout=5 sec
    nifi.cluster.node.read.timeout=5 sec
    nifi.cluster.firewall.file=

```

I set the FQDN of the node I am configuring, and I choose the arbitrary 9999 port for the communication with the elected cluster coordinator. I apply the same configuration on my other nodes:

We have configured the exchanges between the nodes and the cluster coordinator, now let’s move to the exchanges between the nodes (to balance the data of the flows). We have the following properties:

* For master 
```
    nifi.remote.input.host=master
    nifi.remote.input.secure=false
    nifi.remote.input.socket.port=9998
    nifi.remote.input.http.enabled=true
    nifi.remote.input.http.transaction.ttl=30 sec

    nifi.load.balance.host=master

```

* For datanode1

```
    nifi.remote.input.host=datanode1
    nifi.remote.input.secure=false
    nifi.remote.input.socket.port=9998
    nifi.remote.input.http.enabled=true
    nifi.remote.input.http.transaction.ttl=30 sec

    nifi.load.balance.host=datanode1


```

* For datanode2

```
    nifi.remote.input.host=datanode2
    nifi.remote.input.secure=false
    nifi.remote.input.socket.port=9998
    nifi.remote.input.http.enabled=true
    nifi.remote.input.http.transaction.ttl=30 sec

    nifi.load.balance.host=datanode2

```
Again, I set the FQDN of the node I am configuring and I choose the arbitrary 9998 port for the Site-to-Site (S2S) exchanges between the nodes of my cluster. 
The same applies for all the nodes (just change the host property with the correct FQDN).

It is also important to set the FQDN for the web server property, otherwise we may get strange behaviors with all nodes identified as ‘localhost’ in the UI.
Consequently, for each node, set the following property with the correct FQDN:

* For master:
```
nifi.web.http.host=master
nifi.web.http.port=8080

```

* For datanode1:

```
nifi.web.http.host=datanode1
nifi.web.http.port=8080

```

* For datanode2:

```
nifi.web.http.host=datanode2
nifi.web.http.port=8080
```

* run this command on each node on nifi folder

```
./bin/nifi.sh start && tail -f ./logs/nifi-app.log
```

* Now Open the whatever node you want to open on browser for example:
```
192.168.XXX.111:8080 for master
192.168.XXX.112:8080 for datanode1
192.168.XXX.113:8080 for datanode2
```
