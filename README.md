# Project-11:  Ansible configuration management plus jenkins to automate deployment on targeted host/servers

### ANSIBLE CONFIGURATION MANAGEMENT – AUTOMATE PROJECT (7 TO 10)
* Task
* Install and configure Ansible client to act as a Jump Server/Bastion Host
* Create a simple Ansible playbook to automate servers configuration

## INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE

* Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.
* In your GitHub account create a new repository and name it ansible-config-mgt.
Instal Ansible
---
sudo apt update
sudo apt install ansible
---
## Check your Ansible version by running 
ansible --version

4 - Configure Jenkins build job to save your repository content every time you change it – this will solidify your Jenkins configuration skills acquired in Project 9.
* Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.
* Configure Webhook in GitHub and set webhook to trigger ansible build.
* Configure a Post-build job to save all (**) files, like you did it in Project 9.
5 - Test your setup by making some change in README.MD file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder
---
ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/            # ==> <build_number> =1, 2, ....
---

# Step 2 – Prepare your development environment using Visual Studio Code

* First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor. There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs – Visual Studio Code (VSC), you can get it here.

After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.

## Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance
---
git clone <ansible-config-mgt repo link>
---
# BEGIN ANSIBLE DEVELOPMENT
* In your ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature. ( new branch creation= PRJ-145)
* Checkout the newly created feature branch to your local machine and start building your code and directory structure
* Create a directory and name it playbooks – it will be used to store all your playbook files.
* Create a directory and name it inventory – it will be used to keep your hosts organised.
* Within the playbooks folder, create your first playbook, and name it common.yml
* Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.

## Step 4 – Set up an Ansible Inventory
  
* Make sure to setup SSH agent and connect VS Code to your Jenkins-Ansible instance:
---
eval `ssh-agent -s`
ssh-add <path-to-private-key>
### To teserver with ssh:

### Confirm the key has been added after login to server using ssh:
---
ssh -A ec2-user@<Public_iP>

---

### Update your inventory/dev.yml file with this snippet of code:
 ---
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
---
 ### Create a Common Playbook
* In common.yml playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
 ---

## Step 6 – Update GIT with the latest code

* Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the master branch.

* Commit your code into GitHub:
---
* use git commands to add, commit and push your branch to GitHub.
git status

git add <selected files>

git commit -m "commit message"

# Create a Pull request (PR)
---
- Create a Pull request (PR)
- Wear a hat of another developer for a second, and act as a reviewer.
- If the reviewer is happy with your new feature development, merge the code to the master branch.

- Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.

- Once your code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to 
---
LS /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.
---
## Step 7 – Run first Ansible test
* Now, it is time to execute ansible-playbook command and verify if your playbook actually works:
---
cd ansible-config-mgt
ansible-playbook -i inventory/dev.yml playbooks/common.yml
---
* In each of the server pointed at the configuration playbooks, login using ssh and check if wireshark has been installed by running which wireshark or wireshark --version
* Your updated with Ansible architecture now looks like this:
 
 
 * Update your ansible playbook with some new Ansible tasks and go through the full checkout -> change codes -> commit -> PR -> merge -> build -> ansible-playbook
  
  * cycle again to see how easily you can manage a servers fleet of any size with just one command!
  
  
