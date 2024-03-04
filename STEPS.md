# **CI/CD Pipeline using Jenkins & Maven**

In this project, you'll learn how to ssh into EC2 Amazon Linux instance how to install Jenkins & Maven and how to create job to create a CI/CD pipeline. 

### **Step #1: To Create an EC2 Instance**
- To create an instance first navigate using aws management console and click on EC2 service.
- After then click on launch instance and name the instance as **Jenkins_Master** (you can name the instance as per your choice as well).
- You have to select Amazon AMI image for this project.
- We have to create a Key Pair in the key pair tab, Click on Create new key pair and name the key pair as **Jenkins_Key** selct the .pem option because we are going to ssh into that instance using EC2 Instance Connect and then create new key pair option after clicking the keypair will be downloaded into your machine downloads folder.
- We now create a new Security Group name it as **Jenkins_SG** in the description type **Allow port 8080** now will add the inbond rules to allow HTTP(Port 80), SSH(Port 22), HTTPS(Port 443) and Port 8080 to allow traffic from Anywhere on IPV4 traffic by selecting the Add rule in the inbound rules tab and then we will create the Security Group.
-Configure the storage setting and set 20GiB as storage.
- Now we have successfully created the key pair and configured the Security Group for our EC2 Instance now we can launch our EC2 Intance.
- **Note :- It is important to allow Port 8080 because Jenkins uses Port 8080 for its services.**

### **Step #2: SSH into EC2 Instance(Jenkins_Master) and Install Jenkins and Java-JDK11**
- After the Instance is launched and running we will ssh into the EC2 Instance using EC2 Instance Connect by selecting the running instance and then clicking on connect button.
- After clicking on connect we will be under a EC2 Instance Connect tab under that tab there is yellow connect button click on it to connect to EC2 instance Connect.
- A new tab will be opened wait for few seconds to load the terminal.
- As the terminal is loaded we enter following command to get started with the installation process :-
```
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
```
- After entering hit `Enter` above command is to create a repository of jenkins after this we enter the following command :-
```
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
```
- The above command will import the key or rather add the key.
- Next is to install javaOpenJdk11 on the instance running Amazon Linux AMI the command for it is as follows :-
```
sudo yum install java-11-amazon-corretto -y
```
- After entering the command hit `Enter` and there will be a prompt in the installing telling you to enter `[Y/n]` enter `Y` means yes to continue with the installation.
- After installation is done we can check whether java is installed by simply typing :-
```
java -version
```
- The above command will show us the output as displaying the java version (i.e 11).
- **Note :- We have specifically installed java version 11 because jenkins needs java 11, java 17 or java 21 for its installation.**
- Now its time to install Jenkins by using the following command :-
```
sudo yum install jenkins
```
- Hit `Enter` after typing the command it will again show you a prompt then type `y` and `Enter` key to begin with the installation.
- After Jenkins is installed you can verify its installation by typing which will show you the version which is installed on the instance:-
```
jenkins -version
```
- To check for Jenkins Status you can type :-
```
sudo service jenkins status
```
- Which will show you that the service is inactive.
- To start the service we can type :-
```
sudo service jenkins start
```
- After typing and hitting `Enter` we will have to wait for few seconds for the service to start.
- When the service is started it will show `"OK"` that the service has begun and it will return the prompt input.
- Again we can check our status by running the above `sudo service jenkins status` and it will show that it is `active (running).` and your jenkins is up and running.
- **Note :- Before further processing please make sure that your EC2 instance security group has an inbound rule which allows port 8080 for jenkins to access the port.**
- Now we will take our EC2 instance public IP address and copy it and paste it in the browser's new tab and with that we will type our jenkins port which is 8080 for example `56.171.188.90:8080` and then hit `Enter` you will see a window which tells you enter `initialAdminPassword` to get that password we type the following command in our EC2 Instance Connect console :-
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
- The above url /var/lib/jenkins/secrets/initialAdminPassword was copied from the jenkins window open in the browser preceding it with `sudo` and `cat` command.
- After entering the command you will get the password on the Instance Connect console copy it and paste it in the browser's window tab and the click on continue button.
- After this it will show you options to `Install suggested plugins` or `Select plugins to install` we select `Install suggested plugins` option and will start downloading the plugins, we will wait for sometime let the plugins be installed.
- After the plugins are installed it will tell us to create our `Username`, `Password`, `Full name` and `E-mail address` you can enter the details and then click on `Save and Continue` option to continue.
- You have successfully installed jenkins in your system.

### **Step #3: Configuring Jenkins to create our Pipeline**
- Now you can see our Jenkins window is open in that click on Manage Jenkins.
- In that click on Node tab and select it. In this step, we are setting up the Slave Node in Jenkins. It is not a good practice to run your jobs on the master node.
- After clicking on Node Tab click on New Node.
- Give a name according to your preference here i have given **Jenkins_slave_node** and select Permanent Agent and click on create.

### **Step #4: To launch another Instance**
- We now go back to our EC2 console to launch another instance we name the instance as **Jenkins_slaveNode** and attack the same key pair and security group we attached for the **Jenkins_Master** instance and launch the instance.
- Now connect the Instance using EC2 Instance Connect as we did for the **Jenkins_Master** instance and create a directory using the following command :-
```
mkdir jenkinaws
```
- We are creating this directory as we need its path to configure our **Jenkins_slave_node** in jenkins.
- Now we change the directory using :-
```
cd jenkinaws
```
- To copy the path of the current working directory enter the following command :-
```
pwd
```
- We have got the path copy the path and proceed for the next step.

### **Step #5: **Configuring Jenkins_slave_Node**
- Now in Jenkins window set the number of executioners to '2' or according to your need and paste the path which we have copied from the current working directory in previous **Step:4.**
- As we know when we run the job Jenkins copy all the files and folders to its workspace. So after giving the path of remote root directory jenkins will go the given path and the directory will act as a jenkins workspace.
- Now scroll down and select launch method as launch Agent via SSH.
- Now we have to enter Public IP of out master node in the Host section for that navigate back to EC2 Console select the **Jenkins_Master** instance and under the instance there will be a panel in which we will find our Public IP address of our instance.
- We do this as Jenkins_master node will control the slave node.
- For connection of slave node to master node jenkins needs id and password , So to add the credentials click on add credentials and Select SSH username with private key option.
- As we have launched our instances in AWS, AWS provides a username(ec2-user) and password in the form of a key.
- The key was downloaded when we first launched our **Jenkins_Master** instance.
- Enter the username as ec2-user and then select the 'Enter directly' option.
- Copy the contents of the key which we have downloaded it will be in the downloads folder in our machine open the file using notepad or any other editor copy the contents and paste it inside the 'Enter directly' option and then click on add.
- In the host key verification strategy select Non Verifying verification strategy, so while ssh login it won't ask for Yes/No permissions.
- Now we have successfully configured the slaveNode click on slave node and it will be launched.
- **Note :- You need to have installed java-openjdk11 in your both master and slave instances before launching the agent.**

### **Additional Commands**
- If you want to check docker container than we can run a shell inside container by typing `docker container exec -it control_cursor bash`.
- In the above command `control_damage` is an random name of the container created in your case it will be different.
- We have entered into the shell inside container now we use `ls` command we will see all the files of our website listed.
- You can also check for Nginx status by typing `service nginx status` it will show you the output as nginx is running.
- Now you can exit the shell inside container by using `exit` command. 

- #### Your Project is Complete.

### **Project Completion Time**
- Approximately 15-20 minutes.

