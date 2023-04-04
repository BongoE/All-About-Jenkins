Master-Slave Configuration is one of the most important aspects of Jenkins. 


## Scenario:
* Parallel running Jobs consume the Data-Volume of EC2 Instances
* The Jenkins server can go down at anytime!
* How do we provision for such scenarios? The aspect of auto-scaling some to mind. Creation of additional EC2-Instances.
* This concept in Jenkins is called **Master-Slave configuration**. Creation of Additional machine (EC2-Instances) called Slave-Machines (S-M)
* The Developer Instance is called the Master-Instance in this scenario.
* The Slave Instance is meant to support high Through-put and provide high availability.
* S-M is merely a replica (Developer-Like-Instance)

Master - Slave configuration
------------------------------------

### Prerequisites:
* Same version of java should be installed on both Master and Slave Machine.
* Master and slave should have password less SSH

## Step 1- Create and  connect to Slave-Machine (Slave-Instance)

1) Create an EC2-Instance and grant ssh persmission 
2) Update the apt repository
```
sudo apt-get update
sudo apt install openjdk-8-jdk -y (Install the latest or recommended version of Java)
```
3) Check the Java Version
```
java -version
```

## Step 2- We need to establish password less connection between Development Server and Slave Machine


4) Connect to slave (establish an ssh connection)
5) Check  user
```
$ whoami       (  Name of EC2 AMI would be revealed. I used ubuntu )
```

6) Set password  for  ubuntu  user
```
syntax:
sudo  passwd  <user_name>
Ex:        sudo passwd ubuntu
enter password
(I used Ubuntu as my username and password for simplicity during learning. Use credentials that are simple for learning purposes)
```
7) Enter the configuration file
 ```
$  cd /etc/ssh

$ ls   ( To get list of files )   Look for   sshd_config
```

8) Edit sshd_config
```
$ sudo vim sshd_config

Go to insert mode
Change password authentication to 'yes'
Save and quit with the command :wq
```
9) Restart the service
```
$ sudo service ssh restart
```

## Step 3- Let's Test The Connection

10) Connect (ssh connection) to the development server  ( Master )

11) Connect to slave server through Dev server (master)
```
ssh ubuntu@private_ip_slave_machine
```
View connection with the slave Machine...successful!!. 

exit  ( to come back to master )



17)  To connect to slave  without password
$ ssh-keygen      ( In master)


18) copy the keys to slave server
ssh-copy-id  ubuntu@private_ip_slave_server
ssh-copy-id  ubuntu@172.31.1.107



19) now we are able to connect to the slave user without password
$  ssh ubuntu@172.31.1.107



-------------------

Download slave.jar in slave machine


sudo  wget http://172.31.41.7:8080/jnlpJars/slave.jar



Check the file is download or not
$ ls

check the file permissions
$ ls -l

we want    rwxrw-r--

3) Give execute permissions of this file
sudo chmod u+x slave.jar



4) Create an empty folder which will work like workspace for jenkins to use on the slave machine
$ mkdir workspace
$ cd workspace
$ pwd         ( note the path of the workspace )--    /home/ubuntu/workspace



++++++++++++++++++++++++++++




Creating node in Jenkins
---------------------------

 Open the dashboard of jenkins

manage jenkins --- manage nodes

7) Click on new node ----  node name -  Myslave
		       - select permanent agent

Remote root directory   -/home/ubuntu/workspace
Label - Slave_lab


10) Go to Launch method
Select Launch agent via execution of command on the controller

11) In Launch command
ssh ubuntu@private_ip_of_slave java -jar slave.jar
ssh ubuntu@172.31.1.107     java  -jar   slave.jar


13) Click on save

++++++++++++++++++++++++++++++
Configure job to run on slave
------------------------------

14) Select Testing Job

15) Go to Configure  --> General Tab

17) Check Restrict where this project can be run

18) Enter Label Expression ( Slave_lab)

Apply ---> Save  
Run the job, In console output, we can see the job is executed in slave machine
+++++++++++++++++++++++++++++++++++++
