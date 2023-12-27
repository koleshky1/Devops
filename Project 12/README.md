# ANSIBLE DYNAMIC ASSIGNMENTS (INCLUDE) AND COMMUNITY ROLES


 The project  introduces dynamic assignments by using include module.

Static assignments use import Ansible module. The module that enables dynamic assignments is include.


Note: Depending on what method you used in the previous project you may have or not have roles folder in your GitHub repository – if you used ansible-galaxy, then roles directory was only created on your Jenkins-Ansible server locally. It is recommended to have all the codes managed and tracked in GitHub, so you might want to recreate this structure manually in this case – it is up to you.

# STEP 1 New branch Creation.

Create a new branch and name it `dynamic-assignments`.

`git checkout -b dynamic-assignments`

![Alt text](<images/new branch creation.png>)

![Alt text](<images/dynamic assingment branch creation on github.png>)

Create a new folder called `dymanic-assignments` folder

`mkdir dynamic-assignments`

![Alt text](<images/dynamic assignment folder creation.png>)

![Alt text](<images/dynamic assignment folder1.png>)

Create a file in `dynamic-assignments` folder and name it `env-vars.yml`

![Alt text](<images/env vars yml creation1.png>)

![Alt text](<images/env vars yml 2.png>)

The floder structure should look like this:

![Alt text](<images/structure 1.png>)


# STEP 2 Env-vars folder and files creation

Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc., we will need a way to set values to variables per specific environment. For this reason, we will now create a folder to keep each environment’s variables file. Therefore, create a new folder env-vars, then for each environment, create new YAML files which we will use to set variables. Your layout should now look like this.

Create a folder and name it `env-vars`

`mkdir env-vars`

![Alt text](<images/env var  folder creation.png>)

![Alt text](<images/env vars folder 1.png>)

Create the variable files within the env-vars folder

`touch dev.yml prod.yml stage.yml uat.yml`

![Alt text](<images/env vars variable files creation.png>)

![Alt text](<images/env vars variable files creation 1.png>)


The layout should look like this now.

![Alt text](<images/structure 2.png>)


# STEP 3  Snippet paste to env-var.yml file

Now paste the instruction below into the env-vars.yml file.

---
- name: collate variables from env specific file, if it exists
  hosts: all
  tasks:
    - name: looping through list of available files
      include_vars: "{{ item }}"
      with_first_found:
        - files:
            - dev.yml
            - stage.yml
            - prod.yml
            - uat.yml
          paths:
            - "{{ playbook_dir }}/../env-vars"
      tags:
        - always

![Alt text](<images/code paste into env vars.yml.png>) 


Notice 3 things to notice here:

We used include_vars syntax instead of include, this is because Ansible developers decided to separate different features of the module. From Ansible version 2.8, the include module is deprecated and variants of include_* must be used. These are:
include_role
include_tasks
include_vars
In the same version, variants of import were also introduces, such as:

import_role
import_tasks
We made use of a special variables { playbook_dir } and { inventory_file }. { playbook_dir } will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem. { inventory_file } on the other hand will dynamically resolve to the name of the inventory file being used, then append .yml so that it picks up the required file within the env-vars folder.

We are including the variables using a loop. with_first_found implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment specific env file does not exist.

# STEP 4 Update to site.yml

Update site.yml with dynamic assignments
Update site.yml file to make use of the dynamic assignment. (At this point, we cannot test it yet. We are just setting the stage for what is yet to come. So hang on to your hats) site.yml should now look like this.

---
- hosts: all
- name: Include dynamic variables 
  tasks:
  import_playbook: ../static-assignments/common.yml 
  include: ../dynamic-assignments/env-vars.yml
  tags:
    - always

-  hosts: webservers
- name: Webserver assignment
  import_playbook: ../static-assignments/webservers.yml

![Alt text](<images/site.yml code update.png>)

![Alt text](<images/git add and push.png>)

![Alt text](<images/git push to github.png>)

![Alt text](<images/Pull request and merge to main.png>)

![Alt text](<images/merged to main.png>)


# STEP 5 Community Roles

Now it is time to create a role for MySQL database – it should install the MySQL package, create a database and configure users. But why should we re-invent the wheel? There are tons of roles that have already been developed by other open source engineers out there. These roles are actually production ready, and dynamic to accomodate most of Linux flavours. With Ansible Galaxy again, we can simply download a ready to use ansible role, and keep going.

Download MySQL Ansible Role
We will be using a MySQL role developed by geerlingguy. Hint: To preserve your your GitHub in actual state after you install a new role – make a commit and push to master your ‘ansible-config-mgt’ directory. Of course you must have git installed and configured on Jenkins-Ansible server and, for more convenient work with codes, you can configure Visual Studio Code to work with this directory. In this case, you will no longer need webhook and Jenkins jobs to update your codes on Jenkins-Ansible server, so you can disable it – we will be using Jenkins later for a better purpose.

On Jenkins-Ansible server make sure that git is installed with git --version, then go to ‘ansible-config-mgt’ directory and run

git init
git pull https://github.com/<your-name>/ansible-config-mgt.git
git remote add origin https://github.com/<your-name>/ansible-config-mgt.git
git branch roles-feature
git switch roles-feature
git init

Inside roles directory create your new MySQL role with ansible-galaxy install -p . geerlingguy.mysql and rename the folder to mysql using mv geerlingguy.mysql/ mysql

Read README.md file, and edit roles configuration to use correct credentials for MySQL required for the tooling website. Now it is time to upload the changes into your GitHub:

git add .
git commit -m "Commit new role files into GitHub"
git push --set-upstream origin roles-feature
Now, if you are satisfied with your codes, you can create a Pull Request and merge it to main branch on GitHub.

Load Balancer roles
We want to be able to choose which Load Balancer to use, Nginx or Apache, so we need to have two roles respectively:

Nginx
Apache
ansible galaxy

Important Hints:

Since you cannot use both Nginx and Apache load balancer, you need to add a condition to enable either one – this is where you can make use of variables.
Declare a variable in defaults/main.yml file inside the Nginx and Apache roles. Name each variables enable_nginx_lb and enable_apache_lb respectively.
Set both values to false like this enable_nginx_lb: false and enable_apache_lb: false.
Declare another variable in both roles load_balancer_is_required and set its value to false as well
Update both assignment and site.yml files respectively
loadbalancers.yml file

- hosts: lb
  roles:
    - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
    - { role: apache, when: enable_apache_lb and load_balancer_is_required }
site.yml file

    - name: Loadbalancers assignment
       hosts: lb
         - import_playbook: ../static-assignments/loadbalancers.yml
        when: load_balancer_is_required 

        x
Now you can make use of env-vars\uat.yml file to define which loadbalancer to use in UAT environment by setting respective environmental variable to true. You will activate load balancer, and enable nginx by setting these in the respective environment’s env-vars file.

enable_nginx_lb: true
load_balancer_is_required: true
ansible i 1

ansible i 3

ansible i 2

The same must work with apache LB, so you can switch it by setting respective environmental variable to true and other to false.

To test this, you can update inventory for each environment and run Ansible against each environment.

Congratulations!

You have learned and practiced how to use Ansible configuration management tool to prepare UAT environment for Tooling web solution.