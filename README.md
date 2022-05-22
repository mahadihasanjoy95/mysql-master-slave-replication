# mysql-master-slave-replication
![](https://github.com/mahadihasanjoy95/mysql-master-slave-replication/blob/68d6075a8699fca271bec0624f45f07916b77f49/MySQL-Configuration-to-Set-Up-Master-Slave-Replication.jpg)
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
When MySQL server starts for the first time, a temporary password is generated for the MySQL root user. To find the password use the following grep command:
```console
sudo grep 'temporary password' /var/log/mysqld.log
```

Run the **mysql_secure_installation** command to set your new root password and improve the security of the MySQL instance:
```console
mysql_secure_installation
```

**_NOTE: Just disabling remote login will be No(n) if you want to login remotely from other servers._**
## Step 2: Configure Master 
First, we will configure the master MySQL server and make the following changes:
- [ ] Set the private ip. [you can set *0.0.0.0* if you want access from virtual ip]
- [ ] Set a unique server ID.
- [ ] Enable binary logging.
To do so open the MySQL configuration file and add the following lines in the [mysqld] section:
```console
sudo nano /etc/my.cnf
```
```
bind-address           = 192.168.7.130/0.0.0.0
server-id              = 1
log_bin                = mysql-bin
```

Once done, restart the MySQL service for changes to take effect
```
sudo systemctl restart mysqld
```

The next step is to create a new replication user. Log in to the MySQL server as the root user:
```
CREATE USER 'replica'@'%' IDENTIFIED BY 'Datasoft1998@';
GRANT REPLICATION SLAVE ON *.* TO 'replica'@'%';
GRANT ALL PRIVILEGES ON * . * TO 'replica'@'%';
ALTER USER 'replica'@'%' IDENTIFIED WITH mysql_native_password BY 'Datasoft1998@';
```
While still inside the MySQL prompt, execute the following command that will print the binary filename and position.
```
SHOW MASTER STATUS\G
```

OUTPUT:
```
*************************** 1. row ***************************
             File: mysql-bin.000001
         Position: 1427
     Binlog_Do_DB: 
 Binlog_Ignore_DB: 
Executed_Gtid_Set: 
1 row in set (0.00 sec)
```

**_NOTE:Take note of the file name, ‘mysql-bin.000001’ and Position ‘1427’. You’ll need these values when configuring the slave server. These values will probably be different on your server._**

## Step 3: Configure Slave 
We have to set up the slave server as master have. 
- [ ] Set the private ip. [you can set 0.0.0.0 if you want access from virtual ip]
- [ ] Set a unique server ID.
- [ ] Enable binary logging.
To do so open the MySQL configuration file and add the following lines in the [mysqld] section:
```
sudo nano /etc/my.cnf
```
add this:
```
bind-address           = 192.168.7.132/0.0.0.0
server-id              = 1
log_bin                = mysql-bin
```

Once done, restart the MySQL service for changes to take effect
```
sudo systemctl restart mysqld
```

Then login to the mysql and First, stop the slave threads:
```
STOP SLAVE;
```


```
CHANGE MASTER TO
MASTER_HOST='192.168.7.130',
MASTER_USER='replica',
MASTER_PASSWORD='Datasoft1998@',
MASTER_LOG_FILE='mysql-bin.000001',
MASTER_LOG_POS=1427;
```

Then start the slave
```
START SLAVE;
```

All configuration done, now you can create a database from the master server, but can see the reflection on the slave server. 
This is the end of master-slave setup.

&copy; Mahadi Hasan Joy
