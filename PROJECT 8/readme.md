# IMPLEMENTING LVM ON LINUX SERVERS (WEB SERVER AND DATABASE SERVER)

## PREREQUISTE

- Two cloud  servers: a webserver and a database server  
- Both servers to run on RedHatLinux os
- Linux Terminal(Git Bash)
- Ability to connect the terminal to the cloud environment using SSH.

# STEP 1 SPIN UP TWO INSTANCES 

Spin up two instance to run on RedHat linux os: a webserver and a database server

connnect the webserver to the terminal by doing SSH

![Alt text](images/sshwb.png)

## Create the volumes for the web server

![Alt text](<images/volumes web server.png>)

Attach the volumes for the web server

![Alt text](<images/attached web server volumes.png>)

# STEP 2 CONFIGURING THE WEB SERVER

## Open the server and use the command to inspect the volumes

`lsblk`

![Alt text](<images/server volume inspection.png>)

- The volumes names are: xvdf, xvdg and xvdh

- To check all mounts and free space on server

![Alt text](<images/all mount check.png>)

## Create single partion for each volume

Volume 1

`sudo gdisk /dev/xvdf`


![Alt text](<images/partition xvdg web1.png>)

Volume 2

`sudo gdisk /dev/xvdg`

![Alt text](<images/partition xvdg web2.png>)


Volume 3

`sudo gdisk /dev/xvdh`

![Alt text](<images/partition xvdh web2.png>)

VIew new partitions on web server

![Alt text](<images/view new partitions.png>)

# STEP 3 PREPARE THE LVM

Use the command to install LVM 2 package on RedHat

`sudo yum install lvm2`

![Alt text](<images/install lvm2.png>)

![Alt text](<images/lvm2 completed.png>)

Verify lvm installation

![Alt text](<images/verify lvm installation.png>)

To check partition availability

`sudo lvmdiskscan`

![Alt text](<images/verify partition creation.png>)

Use the `pv create` command to mark the physical volumes to be used by the LVM

`sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

![Alt text](<images/mark physical volumes.png>)

Verify physical volumes are created

`sudo pvs`

![Alt text](<images/verify volumes.png>)

Add all three volumes to a volume group VG and verify task.

`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

`sudo vgs`

![Alt text](<images/add to vg.png>)

Crete and Verify logical volumes

`sudo lvcreate -n app-lv -L 14G webdata-vg`

`sudo lvcreate -n logs-lv -L 14G webdata-vg`

 `sudo lvs`


![Alt text](<images/create and verify logical volumes.png>)

Verify the entire setup

`sudo vgdisplay -v #view complete setup - VG, PV, and LV`


![Alt text](<images/entire setup verification.png>)

Format logical volumes with ext4 file system

`sudo mkfs -t ext4 /dev/webdata-vg/app-lv`

`sudo mkfs -t ext4 /dev/webdata-vg/logs-lv`

![Alt text](<images/format logical volume.png>)

Create Var/www/html dirctory to store the website files for the app.

`sudo mkdir -p /var/www/html`

![Alt text](<images/create dir for app-lv.png>)

Create home/recovery/logs  to backup previous logged data.

`sudo mkdir -p /home/recovery/logs`

![Alt text](<images/create home log.png>)

Mount the var/www/hmtl on the app-lv logical volume

sudo mount /dev/webdata-vg/app-lv /var/www/html/

![Alt text](<images/mount the var.png>)

Backup all files in the var/logs directory into the home/recovery/logs

`sudo rsync -av /var/log/. /home/recovery/logs/`

![Alt text](<images/backup logs.png>)

Mount var/log on logs-lv logical volume

`sudo mount /dev/webdata-vg/logs-lv /var/log`

![Alt text](<images/mount log.png>)

Verify mount

`df -h`

![Alt text](<images/verify mount.png>)

Restore log files back to var/logs directory

`sudo rsync -av /home/recovery/logs/log /var/log`

![Alt text](<images/restore log file.png>)

Update the `/etc/fstab` file

The intend is to make our configuration persist when we restart our instance.

`blkid `

`sudo vi /etc/fstab`

![Alt text](<images/etc fstab.png>)

To test if configuration is ok

Restart Daemon

Verify if properly done

`sudo mount -a`

`sudo systemctl daemon-reload`

`df -h`

![Alt text](<images/verify .png>)

# STEP 4 INSTALL WORDPRESS

Update the repository

`sudo yum -y update`

![Alt text](<images/update the repository.png>)

Install wget, Apache and its dependencies

`sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`

Start Apache

`sudo systemctl enable httpd`

`sudo systemctl start httpd`

`sudo systemctl status`

![Alt text](<images/start apache.png>)

Install PHP and its Depedencies

`sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm`
`sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-9.rpm`
`sudo yum module list php`
`sudo yum module reset php`
`sudo yum module enable php:remi-7.4`
`sudo yum install php php-opcache php-gd php-curl php-mysqlnd`
`sudo systemctl start php-fpm`
`sudo systemctl enable php-fpm`
`sudo setsebool -P httpd_execmem 1`

![Alt text](<images/php dependencies.png>)

Restart Apache

`sudo systemctl restart httpd`

`sudo systemctl status`

Download wordpress and copy to var/www/html

`mkdir wordpress`
`cd   wordpress`
`sudo wget http://wordpress.org/latest.tar.gz`
`sudo tar xzvf latest.tar.gz`
`sudo rm -rf latest.tar.gz`
`sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php`
`sudo cp -R wordpress /var/www/html/`


![Alt text](images/wordpress1.png)

![Alt text](<images/wordpress 2.png>)

Configure the Security policies for Linux

 `sudo chown -R apache:apache /var/www/html/wordpress`
 `sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R`
` sudo setsebool -P httpd_can_network_connect=1`

![Alt text](<images/SE policy.png>)


# STEP 5 PREPARE THE DATABASE

## Create the volumes for the Database server

Attach the volumes for the web server

![Alt text](<attached volumes-1.png>)

SSH terninal to Database instance

![Alt text](<ssh to databse.png>)

# STEP 2 CONFIGURING THE DATABASE SERVER

## Open the server and use the command to inspect the volumes

`lsblk`

![Alt text](<images/vol check db.png>)

- The volumes names are: xvdf, xvdg and xvdh

- To check all mounts and free space on server

`df -h`

## Create single partion for each volume

Volume 1

`sudo gdisk /dev/xvdf`


![Alt text](<images/partition db1.png>)

Volume 2

`sudo gdisk /dev/xvdg`

![Alt text](<images/partition db2.png>)

Volume 3

`sudo gdisk /dev/xvdh`

![Alt text](<images/partition db3.png>)

VIew new partitions on database

![Alt text](<images/view db partitions.png>)

# STEP 3 PREPARE THE LVM

Use the command to install LVM 2 package on RedHat

`sudo yum install lvm2`

![Alt text](<images/lvm 2 installation db.png>)


To check partition availability

`sudo lvmdiskscan`

![Alt text](<images/partition avail db.png>)

Use the `pv create` command to mark the physical volumes to be used by the LVM

`sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

![Alt text](<images/db markdown pv create.png>)

Verify physical volumes are created

`sudo pvs`

![Alt text](<images/verify volumes db.png>)

Add all three volumes to a volume group VG and verify task.

`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

`sudo vgs`

![Alt text](<images/vg create db.png>)

Create and Verify logical volume

`sudo lvcreate -n db-lv  -L 28G database-vg`

 `sudo lvs`

![Alt text](<images/lv create db.png>)

Verify the entire setup

`sudo vgdisplay -v #view complete setup - VG, PV, and LV`

![Alt text](<images/verify entire db setup.png>)





Create database directory

`sudo mkdir /db`

Format logical volumes with ext4 file system

`sudo mkfs.ext4 /dev/database-vg/db-lv /db`

Mount the database on the db-lv logical volume

`sudo mount /dev/database-vg/db-lv /db `

`Verify mount`

![Alt text](<images/mount db.png>)

Update the `/etc/fstab` file

The intend is to make our configuration persist when we restart our instance.

`blkid `

`sudo vi /etc/fstab`

![Alt text](<images/db file.png>)

To test if configuration is ok

Restart Daemon

Verify if properly done

`sudo mount -a`

`sudo systemctl daemon-reload`

`df -h`

![Alt text](<images/test to ok db.png>)

Update and install MySQL Server on Database

`sudo yum update`

![Alt text](<images/udate db server.png>)

Install MySQL Server 

`sudo yum install mysql-server`

![Alt text](<images/mysql install1.png>)

![Alt text](<images/mysql install2.png>)

Verify the service is running

![Alt text](<images/mysql status.png>)

Restart and enable MYSQL

![Alt text](<images/restart and enable mysql.png>)

Run MYSQL seucre Installation

`sudo mysql_secure_installation`

![Alt text](<images/mysql install1.png>)

![Alt text](<images/mysql install2.png>)

Login to MYSQL

![Alt text](<images/log in to mysql.png>)

Create Database

`CREATE DATABASE;`

![Alt text](<images/Create Database.png>)

Create user, grant User privilege and flush privileges

`CREATE USER `myuser`@`172.31.23.103` IDENTIFIED BY 'password';`

`GRANT ALL ON wordpress.* TO 'myuser'@'172.31.23.103';`

`FLUSH PRIVILEGES;`

![Alt text](<images/Create user.png>)

Show Current Databse

`show databases;`

`exit;`

![Alt text](<images/show current databases.png>)

Edit inbound rule for database server to allow port 3306

![Alt text](<images/edit server inbound rule.png>)

Install MYSQL Client on Webserver

`sudo yum install mysql -y`









