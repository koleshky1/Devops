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

Create the volumes for the web server

![Alt text](<images/volumes web server.png>)

Attach the volumes for the web server

![Alt text](<images/attached web server volumes.png>)

# STEP 2 CONFIGURING THE WEBSERVER

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

# STEP 3 INSTALL LVM 2 PACKAGE

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

sudo mkfs -t ext4 /dev/webdata-vg/logs-lv

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

Update `/etc/fstab` 

`sudo blkid`

`sudo vi /etc/fstab/`

![Alt text](<images/etc fstab.png>)

Test configuratiuon, reload daemon and verify setup.

`sudo mount -a`

`sudo systemctl daemon-reload`

`df -h`

![Alt text](<images/verify web.png>)

INSTALLATION OF WORDPRESSS AND MYSQL DATABASE CONFIGURATION

Update the Repository

`sudo yum -y update`

![Alt text](<images/update the repository.png>)

Install wget, Apache and its dependencies.

`sudo yum install wget httpd php php-mysqlnd php-fpm php-json`

![Alt text](<install dependencies.png>)'

Start Apache

`sudo systemctl enable httpd`

`sudo systemctl start httpd`

![Alt text](<images/start apache.png>)

Install PHP and its Dependencies

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

Download and copy wordpress to `var/www/html`

`mkdir wordpress`
`cd   wordpress`
`sudo wget http://wordpress.org/latest.tar.gz`
`sudo tar xzvf latest.tar.gz`
`sudo rm -rf latest.tar.gz`
`sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php`
`sudo cp -R wordpress /var/www/html/`

Configure SE policies

 `sudo chown -R apache:apache /var/www/html/wordpress`
` sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R`
` sudo setsebool -P httpd_can_network_connect=1`

![Alt text](<images/SE policy.png>)


# STEP 4 CONFIGURING DATABASE SERVER SERVER

## Open the server and use the command to inspect the volumes

`lsblk`

![Alt text](<images/vol check db.png>)

- The volumes names are: xvdf, xvdg and xvdh

- To check all mounts and free space on server

![Alt text](<images/vol check db.png>)

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

VIew new partitions on database server

![Alt text](<images/view db partitions.png>)


# STEP 5 INSTALL LVM 2 PACKAGE

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

![Alt text](<images/verify volumes pvs db.png>)

Add all three volumes to a volume group VG and verify task.

`sudo vgcreate database-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

`sudo vgs`

![Alt text](<images/vg create db.png>)

Create and Verify logical volumes

`sudo lvcreate -n db-lv -L 14G database-vg`

 `sudo lvs`

![Alt text](<images/lv create db.png>)

Verify the entire setup

`sudo vgdisplay -v #view complete setup - VG, PV, and LV`

![Alt text](<images/verify entire db setup.png>)

Format logical volumes with ext4 file system

`sudo mkfs -t ext4 /dev/database-vg/db-lv`

![Alt text](<images/format db fs.png>)

Create Var/db directory to store the website files for the database
`sudo mkdir -p /var/db`

Mount the var/db on the db-lv logical volume

sudo mount /dev/database-vg/db-lv /db

Verify mount

`df -h`

![Alt text](<images/mount db.png>)

Update `/etc/fstab` 

`sudo blkid`

`sudo vi /etc/fstab/`

![Alt text](<images/db file.png>)

Test configuratiuon, reload daemon and verify setup.

`sudo mount -a`

`sudo systemctl daemon-reload`

`df -h`

![Alt text](<test to ok db.png>)

# STEP 7 Install MYSQL on DATABASE SERVER

`sudo yum update`

`sudo yum install mysql-server`

![Alt text](<images/mysql install1.png>)

![Alt text](<images/mysql install2.png>)

Run MYSQL Secure Installation

`Sudo mysql_secure_installation`

![Alt text](<images/MySQL secure installation1.png>)

![Alt text](<images/MySQL secure installation2.png>)

Verify the system status using the below:

`sudo systemctl status mysqld`

![Alt text](<images/mysql status.png>)

Restart and Enable MYSQL

`sudo systemctl restart mysqld`
`sudo systemctl enable mysqld`

![Alt text](<images/restart and enable mysql.png>)

Configure Database to work with Wordpress

`sudo mysql`

`CREATE DATABASE wordpress;`

![Alt text](<images/Create Database.png>)

`CREATE USER `myuser`@`172.31.23.103` IDENTIFIED BY 'password';`

`GRANT ALL ON wordpress.* TO 'myuser'@'172.31.23.103';`

`FLUSH PRIVILEGES;`

![Alt text](<images/Create user.png>)

`SHOW DATABASES;`

![Alt text](<images/show new db.png>)

`exit;`

![Alt text](<images/shown database.png>)

Configure wordpress to connect to remote database

`open port 3306`

![Alt text](<images/edit server inbound rule.png>)

# STEP 8 Install MYSQL Client on the Webserver

`sudo yum install mysql -y`

![Alt text](<images/install mysql client.png>)


Edit the Server configuration file to allow connection from remote user

`sudo vi /etc/my.cnf`

![Alt text](<images/input binding address.png>)

Restart MYSQL Server and check status

`sudo systemctl restart mysqld`

![Alt text](<images/restart db and check status.png>)

`sudo mysql -u myuser -ppassword -h 172.31.23.103`

![Alt text](<images/client to server connection.png>)


Edit the configuration file for the webserver so Apache could access wordpress

TO make Apache  owner

` sudo chown -R apache:apache /var/www/html/wordpress`

![Alt text](<images/apache ownership.png>)

Edit Inbound rule for Webserver for Apache to use port 80

![Alt text](<images/webserver inbound rule.png>)

Edit the `wp-config.php` file in wordpress

![Alt text](<wp config file.png>)

View wordpress on browser

`https://54.226.17.208/wordpress`

![Alt text](images/wordpress1.png)

![Alt text](<images/wordpress 2.png>)

![Alt text](<images/wordpress 3.png>)

NB: I did save the wordpress password unable to get to the Wordpress welcome page

