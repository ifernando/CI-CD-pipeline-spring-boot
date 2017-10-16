### Pre-requisites ####
Virtualbox >= 5.0
Vagrant >= 1.8.0
Ansible >= 2.0 
GIT

#Usage#
git clone https://github.com/ifernando/hello-world.git
cd hello-world
vagrant up

# Steps of execution by Vagrant and Ansible #
.Vagrant will call the ansible provisioner
.The ansible roles will spin up a Virtual machine (Ubuntu Trusty64) using Virtualbox provider via a Hostonly Network
.JDK Ansible role will be execute at first (Java and JDK will be installed)
.Jenkins Ansible role will be executed and the following steps are executed :
   - Jenkins apt repository will be added 
   - Dependency deb's (git and curl) will be installed 
   - Installation of Jenkins on /var/lib/jenkins
   - Installation of Maven
   - Installation of Docker inorder for Jenkins to build and run images as containers to run the springboot code fetched from : https://github.com/ifernando/jenkins-hello-world. The docker will be running on a socket on port: 4342 
   - Installation of jenkins-cli.jar in /opt/jenkins to interact with Jenkins through CLI
   - Upgrades Jenkins , installs required plugins to run the Pipeline and updates the plugins as well 
   - Configures Jenkins port and and the Prefix 
   - Configures a Jenkins user , sets up ssh keypair to access github to fetch the code and obtains a Crumb from the API and stores it as an ansible variable 'jenkins_crumb_token'
   - Sets up 2 Jenkins Jobs called 'pipeline-seed' and 'hello-world-job' which is the pipeline which does the following 
          - Checks out the code from the git repo : https://github.com/ifernando/jenkins-hello-world    
          - Builds the code via mvn , run some tests , run  it in a docker container and packages it as an image
          - Pushes the image to dockerhub public repo , this can be obtained from : docker pull roshaneishara/jenkins-pipeline-hello-world
          

#https://github.com/ifernando/jenkins-hello-world#
    - This repository contains the code provide by FETCHR : https://github.com/talal-shobaita/hello-world plus a Groovy file (build.groovy) , a Dockerfile and a dockerentry file to set some Docker variables required for the tomcat docker image
    - The build.groovy file will be read by the jenkins pipeline  

#Manually run the ansible playbook
 - Make sure you are in the 'hello-world' directory
 - ansible-playbook -vvvv -i ansible.host --private-key=./.vagrant/machines/default/virtualbox/private_key -u vagrant jenkins.yml

# Accessing the Jenkins through your host 
   - http://localhost:9090  (Check Vagrantfile to see the port forwarding from the VM to/from the host)
 
# Accessing the VM through ssh 
   - Make sure you are in the 'hello-world' code checked out directory
   - Then ssh into the VM as follows
          . vagrant ssh
          . sudo su

#If vagrant up fails with an error or ansible playbook fails for some reason , then as a workaround run the ansible-playbook manually
 - Make sure you are in the 'hello-world' directory
 - ansible-playbook -vvvv -i ansible.host --private-key=./.vagrant/machines/default/virtualbox/private_key -u vagrant jenkins.yml

# To destroy the environment (i.e. Destroy the Virtual machine)
 - Make sure you are in the 'hello-world' directory
   - vagrant destroy 
    
