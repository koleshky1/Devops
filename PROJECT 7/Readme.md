# AUTOMATING  LOAD BALANCING WITH NGINX USING SHELL SCRIPTING

## PREREQUISTE

- Two cloud  webservers  instances
- One Load balancer instance
- Linux Terminal(Git Bash)
- Instances running on Ubuntu OS
- Ability to connect the terminal to the cloud environment using SSH.

## Load Balancing is the distribution of loads across multiple servers such that no single server is overloaded. The traffic is spread across multiple servers to ensure optimal system performance. In this project the Nginx load balancer will be automated using shell scripting. This will help simplify the process of manually configuring this. The script will automatically create all that was created manually in the previous project.

# STEP 1 - Set up two instances.

Spin up two instances with the names webserver 1 and webserver 2

# STEP 2 Edit the Inbound rule (port 8000)

Edit webserver 1 inbound rule by openning port 8000 for both webservers 

![Alt text](<images/wb1/open port8000 wb1.png>)

Edit webserver 2 inbound rule

![Alt text](<images/wb2/open port 8000 wb2.png>)

# STEP 3  Connect the Git Bash terminal to the AWS webServer  by SSH.

SSH Connection

Webserver 1 

![Alt text](<images/wb1/wb1 ssh connection.png>)

Webserver 2

![Alt text](<images/wb2/wb 2 ssh connection.png>)

# STEP 4 Create a  script file for each webserver instance

open the  files for the script for both webserver 1 and webserver 2 using the command

`sudo vi install.sh`

# STEP 5 Paste the script into the files 

Wbserver 1 script

![Alt text](<images/wb1/wb1 html file.png>)

Webserver 2 Script

![Alt text](<images/wb2/wb2 html file.png>)

Close the Script

`Esc :wq!`

The Script will do all task of configuring the webservers like:

- Update and installl appache2 in each webserver.

- verify that it is up and running for both servers; check the status.

- Add a new Listen directive for port 8000.

- Change port 80 to 8000 0n the virual host in the file etc/apache2/ports.conf.

- Restart apache2 to load the new configuration.



# STEP 6 Change the file permission to make it executable for both servers

`sudo chmod +x nginx.sh`

Webserver 1

![Alt text](<images/wb1/w1 file permission change.png>)

Websever 2 

![Alt text](<images/wb2/w2 file permission change.png>)


# STEP 7 RUN THE SCRIPT

Run the script with the command inputting the IP of each server.

`./install.sh PUBLIC_IP`

Webserver 1

![Alt text](<images/wb1/w1 script command.png>)

![Alt text](<images/wb1/w1 script run begin .png>)

![Alt text](<images/wb1/w1 script run end.png>)

Webserver 2

![Alt text](<images/wb2/w2 script command.png>)

![Alt text](<images/wb2/w2 script run begin .png>)

![Alt text](<images/wb2/w2 script run end.png>)

Copy the public IP of each instances and paste on the browser as shown:

Webserver 1

![Alt text](<images/wb1/webserver 1 browser page.png>)

Webserver 2

![Alt text](<images/wb2/webserver 2 browser page.png>)

# STEP 5  Configure NGINX as a Load Balancer

Spin Up a new EC2 Instance running on UBUNTU 22.04. 


Open port 80 to accept traffic from anywhere.

![Alt text](<images/Nginx/open port 80.png>)

SSH into the instance

![Alt text](<images/Nginx/load balancer ssh.png>)

Open the NGINX configuration file

`sudo vi nginx.sh`

![Alt text](<images/Nginx/nginx config file.png>)

Paste the NGINX configuration script into the file

![Alt text](<images/Nginx/nginx config file.png>)

Close the script 

`Esc :wq!`

Change the file Permission

![Alt text](<images/Nginx/change file permission  nginx.png>)


Run the Script with the command

`./nginx.sh Nginx PUBLIC_IP Webserver-1 PUBLIC_IP:8000 Webserver-2 PUBLIC_IP:8000`

![Alt text](<images/Nginx/nginx script command.png>)

![Alt text](<images/Nginx/Nginx script begin.png>)

![Alt text](<images/Nginx/Nginx script end.png>)


Paste the Public IP of the NGINX Load Balancer in a web browser. If successful the page below is shown

Verify Nginx passes the webservers.

![Alt text](<images/Nginx/Nginx passes web1 on browser.png>)

![Alt text](<images/Nginx/Nginx passes web2 on browser.png>)





