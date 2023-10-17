# ClIENT SERVER ACHITECTURE PROJECT

## Prequisite 
- Two cloud instances
- Linux Terminal(s)
- Instances running on Ubuntu OS
- Ability to connect the terminal to the cloud environment using SSH and the MySQL utility software.
- SQL queries knowledge.


# STEP 1 - Set up two instances.

Spin up two instances with the names MySQL server and MySQL Client
Connect the Git Bash terminal to the AWS MySQL Server by SSH and the VS code terminal to the AWS MySQL Client

Update  of packages in package manager

`sudo apt update`

![Alt text](<images/sudo apt update.png>)

# STEP 2 INSTALL the MySQL SERVER SOFTWARE

Install the MySQL Server software

`sudo apt install mysql-server -y`

![Alt text](<images/server install1.png>)

Start the MySQL Server with the enable command and check the status

`sudo systemctl start mysql.service`

`sudo systemctl status mysql`

![Alt text](<images/enable and check status.png>)

# STEP 3 INSTALL THE MySQL CLIENT SOFTWARE

Update the Package manager for the MySQL client

`sudo apt update`

![Alt text](<images/client sudo update.png>)

Run the MySQL Client installation package with the command

`sudo apt install mysql-client -y`

![Alt text](<images/client install1.png>)

Check the MySQL Client status

`sudo systemclt status`

![Alt text](<images/client status.png>)

# STEP 4 EDIT THE MYSQL SERVER INBOUND RULES.

By default both Instances are on the same  local virtual network and can communicate with local ip adddresses.

Go to the inbound rules in the MySQl Server' Security Groups and edit the inbound rule

Open TCP Port 3306 as the MySQL Server uses this for communication (MySQL/Aurora)

![Alt text](<images/edit server inbound rule.png>)

To allow for adequate securtiy restrict all traffic but allow access to only the IP address of the MySQL Client

# STEP 5 Run th MySQL SECURE INSTALLATION  ON THE SERVER AND CREATE THE DATABASE

Run the MySQL secure installation

`sudo mysql_secure-installation`

![Alt text](<images/MySQL secure installation.png>)

Log in to MySQL Server

`sudo mysql`

![Alt text](<images/log in to mysql.png>)

Create the MYSQL Remote User

`CREATE USER 'remote_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`

![Alt text](<images/create my SQL user.png>)

Create the MySQL Database

`'CREATE DATABASE server_db;`

![Alt text](<images/create mysql database.png>)

Grant Privilege to Remote User

`GRANT ALL ON server_db * TO 'remote_user'@'%' WITH GRANT OPTION;`

![Alt text](<images/grant mysql databse privileges.png>)

Exit Database

`exit;`

![Alt text](<images/exit mySQl.png>)

# STEP 6 CONFIGURE MYSQL SERVER TO ALLOW CONNECTION FROM REMOTE HOSTS

Run the command:

`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf `

![Alt text](<images/post bind address.png>)

Change the binding address of the server from 127.0.0.0 to 0.0.0.0 to allow remote hosts access.

# STEP 7 CONNECT FROM THE MYSQL CLIENT TO THE MYSQL SERVER

Connect from the MySQL Client  to the MySQL SERVER WITH THE COMMAND

`sudo mysql -u rmote_user -h 172.31.86.90 -p`

![Alt text](<images/client to server connection1.png>)

sudo : grants user root privilege

the IP '172.3186.90' is the private IP address of the MYSQL Server

the flag -p enables password prompt

# STEP 8 CHECK CONNECTION SUCCESS TO MYSQL SERVER FROM MYSQL CLIENT AND RUN SQL QUERIES

Check that the client has successfully connected to the server by logging into MySQL and run the Query

`show databases;`

![Alt text](<images/shown database.png>)

The database above shows success in connection of the MySQL Client to the MySQL Server.

