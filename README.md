# mysql-master-slave-replication
## Prerequisites
We have used two Centos 7 servers.
The servers in this example have the following IPs:
* Master: 192.168.7.130
* Slave: 192.168.7.132

## Step 1: Install MySql
Install mysql on both servers. Here I have installed mysql 8.0.26 on both centos servers.
Link: [Intall Mysql](https://www.mysqltutorial.org/install-mysql-centos/)
Once the installation is completed, start the MySQL service and enable it to automatically start on boot with:

```console
sudo systemctl enable mysqld
sudo systemctl start mysqld
```
