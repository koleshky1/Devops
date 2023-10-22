# IMPLEMENTING LOAD BALANCING WITH NGINX

## PREREQUISTE

- Two cloud  webservers  instances
- One Load balancer
- Linux Terminal(Git Bash)
- Instances running on Ubuntu OS
- Ability to connect the terminal to the cloud environment using SSH.

## Load Balancing is the distribution of loads across multiple servers such that no single server is overloaded. The traffic is spread across multiple servers to ensure optimal system performance.

# STEP 1 - Set up two instances.

Spin up two instances with the names webserver 1 and webserver 2

# STEP 2

Edit webserver 1 inbound rule by openning port 8000 for both webservers ( )

![Alt text](<images/Webserver1/inbound webserver1.png>)

Edit webserver 2 inbound rule

![Alt text](<images/Webserver1/Webserver2/WEBSERVER2 INBOUND.png>)

# STEP 3

Connect the Git Bash terminal to the AWS webServer 1 by SSH and Update  of packages in package manager and install :

SSH Connection

![Alt text](<images/Webserver1/SSH CONNECTION WEBSERVER1.png>)

Update and Installation of webserver 1

`sudo apt update -y &&  sudo apt install apache2 -y`

![Alt text](<images/Webserver1/webserver1 apache 2 install.png>)

Connect the Git Bash terminal to the AWS webServer 2 by SSH and Update  of packages in package manager and install :

SSH Connection

![Alt text](<images/Webserver1/Webserver2/ssh webserver 2.png>)

Update and Installation of webserver 1

`sudo apt update -y &&  sudo apt install apache2 -y`

![Alt text](<images/Webserver1/Webserver2/update and install webserver2.png>)

Verify Apache is up and running for both Webservers

Webserver 1 verification

![Alt text](<images/Webserver1/verification of apache2 running on webserver1.png>)

Webserver 2 verification

![Alt text](<images/Webserver1/Webserver2/verify status webserver2.png>)

# STEP 4 Configure Apache2 to Serve a Webpage showing its public IP address

Apache webserver will be configure to serve contents on port 8000 instead of its regular port 80. The following additional will be done:

- An index.html file will be created. The file will contain the public IP of the EC2 instance.
- The default html file will then be overridden with the new one.

Configure apache to serve content on port 8000

Webserver 1 configuration

Copy and paste the command below to open the editor vi/nano

`sudo vi /etc/apache2/ports.conf `

Add a new Listen directive for port 8000

![Alt text](<images/Webserver1/listen8000 webserver1.png>)

Change port 80 to 8000 0n the virual host in the file etc/apache2/ports.conf 

![Alt text](<images/Webserver1/virtual host 8000 webserver1.png>)

Webserver 2 configuration

Copy and paste the command below to open the editor vi/nano

`sudo vi /etc/apache2/ports.conf `

Add a new Listen directive for port 8000

![Alt text](<images/Webserver1/listen8000 webserver1.png>)

Change port 80 to 8000 0n the virual host in the file etc/apache2/ports.conf 

![Alt text](<images/Webserver1/Webserver2/virtual host 8000 webserver2.png>)

Restart Apache to load new configuration

`sudo systemctl restart apache2`

![Alt text](<images/Webserver1/restart apache webserver1.png>)

![Alt text](<images/Webserver1/Webserver2/restart webserver 2.png>)

Create new html file

open a new HTML file with the command below

`sudo vi index.html`

Switch to the insert mode and paste the html file.

![Alt text](<images/Webserver1/webserver 1 html.png>)

![Alt text](<images/Webserver1/webserver 2 html.png>)

Change the file ownership of the index.html file with the command below:

`sudo chown www-data:www-data ./index.html`

![Alt text](<images/Webserver1/chown webserver1.png>)

![Alt text](<images/Webserver1/Webserver2/chown webserver2.png>)

Override the default webpage of the Apache Server.

- Replace the default html with the new one with the command below

`sudo cp -f ./index.html /var/www/html/index.html`

![Alt text](<images/Webserver1/override ownership webserver1.png>)

![Alt text](<images/Webserver1/Webserver2/override ownership webserver2.png>)

- Restart the webserver to load the new configuration

`sudo systemctl restart apache2`

![Alt text](<images/Webserver1/restart apache webserver1ag.png>)

![Alt text](<images/Webserver1/Webserver2/restart apache again.png>)

- Copy the public IP of each instances and paste on the browser as shown:

`Public IP:8000`

![Alt text](<images/Webserver1/webserver1 Browser page.png>)

![Alt text](<images/Webserver1/Webserver2/web browser webserver2.png>)

# STEP 5  Configure NGINX as a Load Balancer

Spin Up a new EC2 Instance running on UBUNTU 22.04. 

Open port 80 to accept traffic from anywhere.

![Alt text](<nginx/lb port 80 .png>)

SSH into the instance

![Alt text](<nginx/ssh LB.png>)

Update and Install NGINX

`sudo apt update -y && sudo apt install nginx -y`

![Alt text](<nginx/update and install nginx.png>)

Verify NGINX installation

`sudo systemctl status nginx`

![Alt text](<nginx/verify Nginx status.png>)

Open the NGINX configuration file

`sudo vi /etc/nginx/conf.d/loadbalancer.conf`

Paste the NGINX configuration code into the test file

![Alt text](<nginx/nginx modified file.png>)

Test the configuration with the command below:

`sudo nginx -t`sudo rm /etc/nginx/sites-enabled/default

Remove default symbolic link

The intent of the command below is to remove or override the default NGINX default installation page

`sudo rm /etc/nginx/sites-enabled/default`

Restart NGINX to load the new configuration with the command below:

`sudo systemctl restart nginx`

Paste the Public IP of the NGINX Load Balancer in a web browser. If successful the page below is shown

![Alt text](<nginx/Nginx web browser.png>)








