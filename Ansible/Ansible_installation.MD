# Ansible Installation

Ansible is an open-source automation platform. It is very, very simple to set up and yet powerful. Ansible can help you with configuration management, application deployment, task automation.

### Pre-requisites

1. An AWS EC2 instance (on Control node)

### Installation steps:
#### on Amazon EC2 instance

1. Install python and python-pip
   ```sh
   yum install python
   yum install python-pip
   ```
1. Install ansible using pip check for version
    ```sh
    pip install ansible
   ansible --version
   ```
   
1. Create a user called ansadmin (on Control node and Managed host)  
   ```sh
   useradd ansadmin
   passwd ansadmin
   ```
1. Below command grant sudo access to ansadmin user. But we strongly recommended using "visudo" command if you are aware vi or nano editor.  (on Control node and Managed host)
   ```sh
   echo "ansadmin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
   ```
   
1. Log in as a ansadmin user on master and generate ssh key (on Control node)
   ```sh 
   sudo su - ansadmin
   ssh-keygen
   ```
1. Copy keys onto all ansible managed hosts (on Control node)
   ```sh 
   ssh-copy-id ansadmin@<target-server>
   ```

1. Ansible server used to create images and store on docker registry. Hence install docker, start docker services and add ansadmin to the docker group. 
   ```sh
   yum install docker
   
   # start docker services 
   service docker start
   service docker start 
   
   # add user to docker group 
   usermod -aG docker ansadmin
   
   # Enable EC2 uses keys for remote acces
   vi /etc/ssh/sshd_config
   Change PasswordAuthentication Yes
   
   service sshd relaod
   
   #Create ssh keys for ansadmin
   su - ansadmin
   ssh-keygen
   
   #to check the kyes
   cd .ssh
   ls -l  /* id_rsa id_rsa.pub */  /* public key(id_rsa.pub) we should copy to target env. and private key (id_rsa) we should keep it secure.*/
   
   #Copy the keys to docker host system.
   ssh-copy-id ansadmin@[dockhostip]
   
   #to check you should connected to docker host and then exit
   ssh ansadmin@[dockhostip]
   

   ```
1. Create a directory /etc/ansible and create an inventory file called "hosts" add control node and managed hosts IP addresses to it. 
 
### Validation test

   
1. Run ansible command as ansadmin user it should be successful (Master)
   ```sh  
   ansible all -m ping
   ```

### Trouble Shooting

Troubleshooting: Unable to install Ansible
If you are unable to install ansible with "yum install ansible", Please make sure you have installed python first.

then try below command.

sudo amazon-linux-extras install ansible2

### Integrate ansible with jenkins

1) Manage Jenkins-> Configure System-> publish over ssh->
   Add SSH server-> 
