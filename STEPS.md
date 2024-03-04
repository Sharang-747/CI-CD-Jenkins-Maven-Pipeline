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
- After entering the command you will get the password on the Instance Connect console copy it and paste it in the browser's window tab and the click on continue button
- 

  
### **Step #3: Create Docker file with Nginx Configurations**
- Now we initiate Docker by typing command `docker run hello-world` and then hit `Enter` it will show that docker is successfully installed.
- After installing we create Docker File using `vi Dockerfile` command in that we paste the below shell script.

```
FROM nginx:latest
RUN apt-get update && apt-get upgrade -y
RUN apt-get install wget unzip -y
WORKDIR /usr/share/nginx/html
COPY default.conf /etc/nginx/sites-enabled/
ADD https://bootstrapmade.com/content/templatefiles/Ninestars/Ninestars.zip .
RUN unzip Ninestars.zip
RUN mv Ninestars/* .
RUN rm -rf Ninestars Ninestars.zip
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

- After the pasting the above code we hit `Esc` button to get into command mode and then type `:wq` to write and save the file.
- Now we create another file using `vi` command and name it as `vi default.conf` file because the `Dockerfile` is going to copy files to `/etc/nginx/sites-enabled/` this location.
- In the `default.conf` file we paste the code below.

```
server {
        listen 80 default_server;
        root /usr/share/nginx/html;
        index index.html;
        server_name mysite.com;
}
```

- After the pasting the above code we hit `Esc` button to get into command mode and then type `:wq` to write and save the file.
- If we use `ls` command which is used to list the files in that directory we can see the `Dockerfile` and `default.conf` files are present in that directory.

### **Step #4: Create Docker Image and Docker Container**
- To create Docker Image we use command `docker build -t testimg:v1 .` and it will create an testimg for us this process will take few seconds.
- If you want to see whether the Docker Image is created you can type `docker images` to check for image.
- Now we create Docker Container by typing `docker run --rm -d -p 80:80 testimg:v1` in this command we are mapping port 80 to 80 and also using `--rm` to delete the container immediately after it stops running.
- If we type `docker ps` command we can see that our container is running and port 80 is exposed.

### **Step #5: **Deploying the Static Website**
- Now we go back to our EC2 Console and click on our instance in our instance details there will be `Public IPv4 DNS` copy that address.
- After copying paste that DNS address which looks like this (http://ec2-54-234-124-243.compute-1.amazonaws.com/) in new tab of your browser you can see your static website is running.
- #### Your Project is Complete.

### **Additional Commands**
- If you want to check docker container than we can run a shell inside container by typing `docker container exec -it control_cursor bash`.
- In the above command `control_damage` is an random name of the container created in your case it will be different.
- We have entered into the shell inside container now we use `ls` command we will see all the files of our website listed.
- You can also check for Nginx status by typing `service nginx status` it will show you the output as nginx is running.
- Now you can exit the shell inside container by using `exit` command. 

### **Project Completion Time**
- Approximately 15-20 minutes.

