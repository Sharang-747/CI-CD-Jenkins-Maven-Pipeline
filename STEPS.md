# **Static Website on AWS EC2 using Nginx & Docker Containers**

In this project, you'll learn how to create a Static Website using Amazon EC2 service, how to ssh into EC2 Instance & how to create Docker Containers and Docker Image with Nginx Configurations. 

### **Step #1: To Create an EC2 Instance**
- To create an instance first navigate using aws management console and click on EC2 service.
- After then click on launch instance and name the instance as **staticwbinstance** (you can name the instance as per your choice as well).
- You have to select ubuntu AMI image for this project.
- We have to create a Key Pair in the key pair tab, Click on Create new key pair and name the key pair as **testkeystaticwb** selct the .pem option because we are going to ssh into that instance using CLI and the creat new key pair option after clicking the keypair will be downloaded into your machine downloads folder.
- We now create a new Security Group name it as **StaticwbGroup** in the description type **Allow port 80 and 22** now will add the inbond rules to allow HTTP(Port 80) and SSH(Port 22) traffic from Anywhere on IPV4 traffic by selecting the Add rule in the inbound rules tab and then we will create the Security Group.
-Configure the storage setting and set 20GiB as storage.
- Now we have successfully created the key pair and configured the Security Group for our EC2 Instance now we paste the Shell Script File in the Advanced Details Section where a big textbox appears in that paste the below code.

```
#!/bin/bash
sudo apt-get update -y
sudo apt-get install wget curl git vim ca-certificates gnupg lsb-release -y
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update -y
sudo apt-cache policy docker-ce
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
sudo usermod -aG docker <USERNAME>
sudo sudo systemctl start docker
sudo sudo enable start docker
sudo wget https://github.com/docker/compose/releases/download/v2.14.0/docker-compose-linux-x86_64 -O /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

- Then create the instance.
- **Note :- We have to change the username where it is highlighted as <USERNAME> to ubuntu as we are using ubuntu AMI Image.**

### **Step #2: SSH into EC2 Instance**
- After the Instance is launched and up and running we will ssh into the instance using command prompt of windows machine if you are using MacOS/Linux machine you will make use of Terminal or Linux shell accordingly.
- Before we ssh into our instance first we download our static website files from clicking on the link (https://bootstrapmade.com/ninestars-free-bootstrap-3-theme-for-creative/#download) and click on download which will download the ninestars website into our downloads folder.
- After this open command prompt change directory to Downloads using `cd Downloads` command which will change the directory.
- In our Downloads directory we have our website files now we can ssh into our instance by clicking on our instance and then by clicking on connect button then on SSH Client tab we will see ssh command which goes as `ssh -i "testkeystaticwb.pem" ubuntu@ec2-54-234-124-243.compute-1.amazonaws.com` the key name and the IP. address mentioned here will be different for every instance created.
- and the paste the command in the command prompt and hit `Enter`.
- It will ask you with a prompt whether you want to connect or not type yes and then hit `Enter`.
- You have successfully ssh'ed into your instance as you can see the prompt has been changed.  

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

