# Jenkins CI/CD Pipeline Project Architecture (Java Web Application)
![CompleteCICDProject!](https://lucid.app/publicSegments/view/a6ef3233-7dda-483a-a662-d8ec90395ba3/image.png)

# Jenkins Complete CI/CD Pipeline Environment Setup 

***Objectives: To setup one pipeline for Terraform infra deployment ,while another pipeline is setup for application deployment. All of them are hosted within the same Jenkins server and using the same Jenkins continues integration tool.***

Now, go to the console and click on **launch instances**
- name: **Jenkins-server**
- os: **Amazon linux**
- instance type: **t2.medium** {At least t.2 median because the application in this server will use more compute power and also consume more memory}
- Keypair: `select a keypair that you have access to it`
- network settings: Click beside on `Edit` because we want to edit and add the SG
Under "inbound" security group rule
  - Click on **Add security group rule**
  - **Type**        - **Protocol**        - **Port range**        - **source type**
  - custom TCP          - TCP                 - 8080                 - Anywhere
    
Scroll down and click on **advanced details**
under the box of user data,
-	Go to the repo [here](https://github.com/awanmbandi/eagles-batch-devops-projects.git)
-	Switch to the `maves-nexus-sonarube-jenkins-install` branch
-	Inside this branch click on **Jenkins-install.sh**
-	Use the clipboard beside to copy the block of script as a whole form line 1-17.
-	Copy and paste it inside the “user data” box so as to install Jenkins in this instance at creation time.
- Click now on **launch instance** at the bottom
- Now test by copying the “public IP” of this “Jenkins-server” and take it to a new browser. So do
- **44.211.155.53:8080**
-	Now, copy the path in red {i.e The path of the admin password}
-	go and ssh into your Jenkins-server
  - so click to check the box against that instance
  - then click on **connect** at the top
  - Click now on **connect** below to do the SSH via the browser itself.

**[ec2-user@ip-172-31-89-517~]$**

-	Now use Sudo to cat the path. So do
- `Sudo cat <paste that path here>`
- `Sudo cat /var/hib/jenkens/secrets/initial/advice password`
- Now copy the password and take it to your Jenkins UI
- Paste the password in the box of “Administrator password”
- click on **continue**
- click now on **install suggested plugins** (to install some defaults plugins)
  
**(Create first Admin user)**
- Username: **admin**
- Password: **admin**
- confirm password: **admin**
- full name: **admin**
- E-mail address: **admin@admin.com**
-	Click now on **save and confirm**
-	click now on **save and finish**
-	click on **start using Jenkins**
  
# Welcome to Jenkins (Jenkins is used to execute the build and also to integret other tools together)

- click on **new item**
- item name:
- (wait here)
  
- # We need a teriform project that will deploy Our infrastructure. So we need a terraform project. So let's first of all go and create a terraform code base project. So to set up our terraform project:
  
- Go to your GitHub account(Kenneth-lekeanyi) and create a Repo
-	New repository
  - Name: **airbub-infrastructure**
- Make it public
- add a Readme file
- Then click on **create repository**
-	Copy the git URL of this repo under HTTPS
-	Now bring up your V.S code
-	
- Cd into the repository
- Then do `git clone< paste the repo UREL Here>` to clone it.
- Now get into the newly clone Airbnb-infrastructure on the left
- Then create the content of the terraform project. So create this files.
   - **Provide.tf**
   - **Ec2.tf**
   - **Var.tf**
   - **S3.tf**
   - **Jenkinsfile**
Go to this repo and copy the content of this files, then you come and paste them under their respective files here. Repo is 
[here](https://github.com/Kenneth-lekeanyi/airbnb-infrastructure-Jenkins-pipeline-infrastructure-.git)
If you are using a different region, you should edit the region in **provider.tf** to the region that you are using. Also change the Ami. {so Go to the region you are using and grap an ami and bring it here}, Otherwise it will fail.
-	Test it by running
  - terraform unit(successful)
  - Terraform plan(successful)
  - Terraform apply(successful)
Now destroy it by running
  - terraform destroy.
-	Now we cannot push these generated terraform files to github. So add a .gitignore file.
**•	Gitignore**
-	Go to Google and search for “terraform git ignore”
-	Copy that content and paste it inside gitignore
-	 Now do
  - `git status`
-	you see that the file such as **.terraform.lock.hcl** persist. It refuses to be extracted. So copy it and paste inside the **•	Gitignore** file and click “save”
-	Now do
-	`git status`
you will know see that it has been extracted, so it can be ignored, so only those pure files are remaining.
Now push it to your GitHub account. So do,
 - `git add .`
 - `git commit -m "commit message"
 - `git push`
 - 
Now we currently have our terraform project for our infrastructure code base. Now we have to somehow create a pipeline that can run this code base.
Now go to the Jenkins dashboard. We want to create a pipeline now.

o	In the Jenkins UI click on “**new item**”
- Pipeline name:Airbnb-infra-deployment-pipeline
- Now click on pipeline to select it
-	then click on "OK" to create the pipeline
-	Scroll down to the section called "pipeline”
  - Definition
  - Script
    - click on the far right on “try sample pipeline” and 
    - select “**hello world**” to see this script(ready made script)
-	click on “**apply**” and click on “**save**”
-	Now click on “**build now**”
you will see a stage view as a sample pipeline and it logs.

# (B) let's install terraform plugins and install terraform itself directly using Jenkins without installing it manually in the O.S
-	so go to Jenkins dashboard let's first install the terraform plugins
-	click on "**manage Jenkins**”
-	now click on "**manage plugins**”
-	click now on "**available**" at the top. so go to "**available plugins**"
in the filter box that appears type “terraform” to populate it.
-	Then check the box on terraform
-	then click on “install without restart”
-	
Now, go run the Build to see if the terraform init stage of the pipeline will go through.
- (Meaning that insert the terraform version and the terraform init in the testing hello world script to test and see if  terraform init will go through. Of course it will fail because despite the fact that we have installed the plugins we have not yet installed terraform itself.)
- 
-	Now go to the dashboard and click on “**manage Jenkins**”
-	then click on “**global tool configuration**”
-	Now, screw down to the terraform
-	click now on "**Add Terraform**”
  - go to Google and search for "**terraform plugins**” so as to see the plugins and the current version which is now at 0.06.
- Name: **Terraform**
- install automatically. (check this box to install the latest version of terraform 1.3.4 linux(386) to be in accord with our O.S version)
- now click on ”**Apply**” and “**Save**”
-	now go back to the ***Airbnb-infrastructure-pipeline*** and build again.
- fail again
- 
-	now click on “**configure**”.
- In the pipeline, insert this short code this is the pipeline script to call terraform.
- The tools installation stage is
- Pipeline
    - agent any
    - tools {
        - terraform "Terraform"
          }
-
-    {This stage "Declarative tool" install will come up automatically when we inject the tool in the code. You can use this system to install Java, maven etc}

-	now click on "**Apply**" and "**save**"
- now run the **build now**, you will see that the terraform init stage will succeed since we have used the script to install terraform. Now our terraform project is configured.
o	Now run “**terraform plan**”
- (it filled because the jenkins instance is unable to authenticate or access our AWS console. So we need a Role to give it that access).
- It fails and complains that “No credentials”. So we shall not create the credential in the Jenkins UI since we cannot pass the credential here. So we shall go and create a Role. So,
-	Go to the IAM console to quckly attach a Role. So
-	Click on “**Roles**”
-	Click on “**create roles**”.(Because it is EC2 instance so we shall attach it to the Jenkins instance)
-	check the box on AWS services” Jenkins EC2 instance.
-	Check the box of EC2
-	click now on “next”
-	type “administrator” in the box and select “administratorAccess” here
-	Scroll down and click on "next"
-	Name: **Jenkins-cicd-server-role**
-	Scroll down and click on "create role”
- now that the rule is created, go to see the Jenkins instance (Jenjins-server) in the EC2 console, and click on it
-	now go to action at the far top right and click on the drop down
-	then click on “security”
-	then click on” modify IAM role”
-	now on the page in the box, click on the drop down and search for the name ***Jenkins-cicd-server-role***
-	then click on “update iam role”
-	
Now go to the pipeline and click on "**built now**” you will see that terraform phase has passed
- but it fails at the level of “terraform apply” this is because of the lack of approval stage so
- go to the script now and inject the “auto-approve stage”. so as to apply it
-	then you click on “**apply**” and then “**save**”
-	then click on "**Build now**”
-	
- Now go to the console and check to confirm that the EC2 instance has been created.
- So this is how to thousands of resources can be created through the Jenkins terraform pipeline.
-	Now insert the validate stage in the script then you run the build again.
-	Then you insect the manual apporal Stage by changing the name and validates to manual approval.
-	Now on the script page, Scroll down to locate “**pipeline syntax**” click on it and open it in a new tab.
-	
- In the box search for “**input: wait for interactive input**” and select it
- Message: **Deploy the infrastructure**
- then click on “**generate pipeline script**”
- Now copy the line of script that appears below and go and insert In your manual approval stage
-	now click on “**apply**” and “**save**”
-	Then click again on “**build now**”
- Now the manager or lead will go to the plan stage check and see that the code matches with what is expected.
-	Then he will hover his moose on top of “***manual approval***” stage And click on “***approval and proceed***"
-	 
Now the infrastructure pipeline is complete and successful. But we Need to see that code or project So as to scan the code.
- { Note that Sonarqube only scans programming languages, while **Chechov** scans the Terraform Scripts.}
- So, insect the “Checkov scan” step in the script

- When you run built now it fails at the checkov scan installation stage. It is complaining that we are installing it inside Jenkins and not in the OS itself. So to fix thi,
-	go to the “Jenkins-server” and SSH into it.
-	Then execute this command there so as to allow Jenkins to do the thing at the sudoers level. So run this command to first open the sudoers file. 
`Sudo nano /etc/sudoers`
- Now, inside the sudoers file, use your down arrow to go down and enter to create some space in any location inside this file.
- Now paste this command there to give all access and the Jenkins user
`jenkins ALL=(ALL) NOPASSWD: ALL`
- Now come out of the namo editor by doing
   - `control+x`
   - Then press `Y`
   - then press `enter`
- Now as we have changed some things in the sudoer file, Restart Jenkins, so do
`sudo systemctl restart Jenkins`
- Now go to the Jenkins UI and refresh since we have restarted it for it to pick up the changes
-	Then sign into Jenkins again
-	Now click on “**build now**”
- It fails again at the check or stage
- it fails because of these:
1)	Insert monitoring in the ***EC2 instance.tf*** to fix the monitoring issue
2)	Also it complained that we optimize **EBS**. So also insect EBS optimized in your **EC2 instance.tf** as well
3)	Ensure EBS values is encrypted as well to fix it further
   
- Now change your directory by getting into the Airbnb-infra. So do 
`Cd Airbnb-infrastructure`
-	Now run the git commands to add it. So do
  - `git add .`
  - `git commit -m “scan fix for Best practices”`
  - `git push`
  - 
-	Now go to Jenkins and click on “**build now**” again.
- It fails again
- 
- So to fix it we can skip it since it is not a major thing.
- Insert the skip-check according to the recommendtions of the logs.
- 
- { **Note**; the --skip—check CKV_AWS=79 that you see in the script will differ from your own scenario. So the checkov will display some logs, follow It to get your own --skip_check. some person will have 2 things to skip and in that way ,it will be.
***Checkov-d . --skip-check CKV_AWS-79, CKV2_AWS_41***}
-	Now click on “**Apply**” and click on “**save**”
-	Then you click on “**Build Now**”
-	{Note: The statefile will be stored in S3 Remote Backend}
- So the infrastructure pipeline is complete. Once we push any changes or New code to our Github airbnb-infrastructure Repo, that infrastructure will automatically get deployed in our AWS account.
-
- Now go to the terminal and get into this directory.
`cd /var/lib/Jenkins/workspace/airbnb-infra-deployment-pipeline`
- Now do `ls` you will see now the files.
- Now,do destroy
`sudo terraform destroy` {if you want to destroy the infrastructure that is just created, ( we could automate it in the pipeline and comment it)}.
- It fails: {Reason is because we install terraform via Jenkins. Terraform is not installed in the O.S.
- So we can destroy it only in the terraform script. so you can go to the script and change the apply to destroy like this.
`sh terraform destroy --auto-approve`
-	Then click on “**save**” and “**apply**”
-	Then go to Jenkins and click on “**build now**”
-	Go to the instsnces and reload to see that the created instance is destroyed.
- Now our infrastructure is done. We shall now proceed to the Jenkins. Application deployment.
- ***Wherein we can deploy a terraform code in the pipeline to authomatically provision the resources in the console via the pipeline.***

- 
# (C)	The Application Deployment Pipeline. 
-
- ***Now, we are going into the Application Deployment Pipeline after completing the infrastructure deploymenr pipeline. So, we shall take the application code and deploy it ontop of servers once all the stages are Completed. This will automaticslly deploy a simple Javo application.***
- Lets first of all setup the Codebase.
-	Get this Repo into your local and to your Remote. This is the Java application project that we are using. (for windows, change the terminal to bash) For macOs, it is o.k. No need to change anything. The Repository is [here](https://github.com/Kenneth-lekeanyi/CI-CI-Pipeline-Jenkins-Prometheus-Grafana.git)
-	
-	Clone the repository to your local and Push it to your company Remote Repo 
-	Now go to the Jenkins UI. 
- lets now create a new pipeline (purposely for this Java Application deployment)
So,
-	Click on “New item”
- Name:**Airbnb-app-deployment-pipeline**
- (so one pipeline is for infra deployment, while another pipeline is for Application deployment. All of them are Hosted within one unique Jenkins server.)
-	Click to select pipeline
-	Click on OK
-	Now scroll down under “**General**” to locate "Pipeline"
-	
- ***{If your pipeline script is in a file like Jenkins file in Github, under “Definition” Jenkins
-	Use the drop down to select “pipeline script from SCM"
  - SCM: **git**
  - Repository URL: Paste the git reposition of that Repo here
  - Branch: Repo of the branch is [here](https://github.com/cvamsikrishna11/Jenkins-CI-CD-Pipeline-Project.git)
  - Branch: main (*/main)
  - Script path: **Jenkinsfile**
  - Click on "**Save**
  - Then go click now on "**Build Now**" to test.
  - Script: Under "Try sample Pipeline", use the drop down to select for "**Hello world**"
  - See the sample script
- Click now on "**Apply**" and "**Save**"
- Click again now on “**build now**” to invoke the build. {You will see the sample pipeline succeed}
* if you want to edit your pipeline, you click on “**configure**” at the left and scroll down to the script and
* Now, directly under it, click on “**pipeline syntax**” to open it on a new page.
- In the {sample step} box on the page that comes up, click inside to search for “git”. Our intension is to search for git clone.
- scroll down to locate and click on “**git: Git**”
- 
Repository URL: Past the git Repo URL of the App repo here. The git repo of this Application in this demo is [here](https://github.com/Kenneth-lekeanyi/CI-CI-Pipeline-Jenkins-Prometheus-Grafana.git)
-	Branch: **main**
-	Since it is a public Repo, we don’t need to add any credentials
-	Click now on “**Generate pipeline script**”
- Now copy the line of code that pop up in box and take it to the sample script and paste it inside the step of git checkout.
- So it appears like this: link to this Jenkinsfile script is [here](https://github.com/cvamsikrishna11/Jenkins-CI-CD-Pipeline-Project.git)

set upSonarQube
to set up sonarqube, access the EC2 console then click on “launch instance”
Name: sonarqube-server
O.S Ami:ubuutu
Version ubuutu 22.04 LTS
Instance type: t2.medium
Keypay: select your keypay
-	network settings click on “edit”
-	Scroll down and click on “add security group rule”
Type.             Protocol.      Port range.     Source type
Custom type  TCP.             9000.             Anywhere
-	click now on advanced detail
-	Scroll down to “user data”
go to the Maven-nexos-sonarqube-Jenkins-install” branch and go to the “sonarqube-install.sh” or use this link to these and case the copy clipboard botton to copy the installation script and paste it inside this user data.
https://github.com/amanmbandi/eagles-batch-devops-projects/blob/maven-nexus-sonaquobe-jenkins-install/sonarqube-install.sh
click now on”launch instance”
take the public IP of this sonarqube server and access it on port 9000
3.82.52.240:9000
- Click on login in the sonarqube UI
- enter the default credential of sonarqube (Which is admin,admin)
Username:admin
Password: admin
click now on “login”
•	Now let's create our Java application project in sonarqube. So click in the sonarqube UI on “create new project”
-	project key: JavelwebApp
-	display name: JavelWebApp 
-	click now on “set up”
Generated a token:
Jenkins secret. Now click on “generate”
-	click on continue
-	what is your project main language?
-	Click to on “Java”
-	You are developing primarily in Java; What is your build technology?
-	Click on “Maven”
Copy this sonarqube code and save it somewhere in your text editor as a backup. (This code will be used to invoke the scanning)
replace the IP in the code with the private IP of the sonarqube Since it is in the VPC with the Jenkins server.
•	Now go to the Jenkins and duplicate the Jenkins screen.
-	Then on the first tab click on “configure”
-	Now, Move down to the script section
-	insect the sonarqube stage with the code carrying the private IP that we inserted.
When you insert the sonaqube code in the script on click on “apply” come back to the sonaqube UI and refresh
you will see that it says
PASSED
it will do the scanning and start displaying the result in its dashboard.
Now proceed install those plugins mention from line 83 in red.
(d) Let's proceed to set up nexos
go first of all ssh into the Jenkins server.
At this time to the real SSH. So,
-	click on the instance by checking the box beside the Jenkins server
-	then click on connect
-	in the “connect to instance” Box that opens, click on “ssh client”
-	copy the chinood command in no3 and run it in the Jenkins server terminal.
Chinod 400 kenneth-keypair.pen “enter”
-	Now copy the command below. Example
Ssh-I “Kenneth-keypair.press”ec2-user@ec2-3-86-99-187.compute-1.amazonaus.com
And go run it's in that Jenkins terminal too “enter”
-	type yes “enter”
[ec2-user@ip-172-31-89-157-]$Now first of all go and add the stage in the script call “upload artifact to Nexus”
* Now launch the Nexus server
o	click on “create” or “launch instances”
         - name: nexus-server
         - O.S :Amazon linux
         - instance app:t2-medium
        - Keypair: select your keypair
o	Network settings. Click on edit
Scroll down and click on “add security group rule”
Type.                Protocol.   Port Range.       Source type
Custom TCP     TCP.           8081.                 Anywhere
-	Then click on “advanced details”
Scroll down to the box of user data.
Go to the maven-nexus-sonarqube-jenkins-install branch and get into the “nexus-install.sh” here is the link
Github.com/amanbnbandi/eagles-batch-devops-projects/tree/maven-nexus-sonarqube-jenkins-install
o	Use the clipboard to copy the script and paste it inside the user data box
o	now click on “launch instance” at the bottom.
Now, access the Nexus UI on 44.199.230.232:8081
Nexus UI comes up
now click on “sign in”
username:admin
password: copy the path given, go and login to Nexus server,then do
sudo cat <paste the path> enter
Copy the password and paste it here. Enter
-	click on sign in
-	click on next
New password: admin
Confirm password: admin
-	Click on “next”
-	on the “configure anonymous access” pop
click on a tick disable anonymous access
-	Then click on “next”
-	then click on “finish”
If you click on browse you will see existing reposition
we are going to create a separate repo for our project. So,
-	click on “settings”
-	then click on “reposition” at the left
-	Click on “create repository”
It asks what kind of artifacts are you going to host here?
Click to check the box on Maven2 camp
Name: maven-project (Note we are going to use this name after)
member repositions
-	Click to select “Maven snapshot”
-	Then click on the greater than > option beside it to add it as a member
-	select or click again on “Maven public”
-	Then click on the greater than > options to add it as a member
-	Select “Maven releases” And do the same thing
-	Select “maven central” and also do the same thing.
Then click on “create repository”
Not that it is Jenkins that needs to initiate the communication and interact with nexus.
-	So ssh unto the Jenkins-server
[ec2-user@ip-172-31-89-157-] $
Now, lets install maven. So
-	First of all run the first command. Sudo uget---(see readme) imd inside “cicd-terraform-infra-deployment-and-application-deployment-pipeline” enter
-	Now copy the next that start with sudo sed---maven repo "enter”
-	Now run this command to actually install Apache maven sudo yum---apache-maven
-	Now click the version to ensure its exist so do maven—version
Now, encrypt Nexus password and update it in the site below. So do maven-erup admin ‘enter’. ( this will encrypt the admin master password of Nexus so that hackers can not crack)
You will see time runny ‘on understandable words and iders.
Copy all what is inside the code bracket
{p89stwp1AMATHsiEo---} copy all this and save it somewhere that you can access it. We shall use it later on.
So the encrypted password is only possible if we have maven installed. That is the reason we have first installed maven.
-	Now change to root user.so do
Sudo su “enter”
-	Then get into that root directory. So do
Cd/root “enter”
-	Now why inside root directory, Create a file called ‘.m2’ so do mkdir.m2 ‘enter’
-	Now get into that .m2 directory. So do 
Cd .m2
-	Now create a settings-security file and vi into it.so do vi settings-security.xml “enter”
Then go copy that encrypted password that was saved somewhere in your text editor and replace in this {admin} place in the code.let it appears as follows
<2xml version=”1.0”?>
<settings security>
<master>{p89stwp1AMATHsiEo---=}</master>
</settingsSecurity>
-	Then save and quite :wql after pressing esc
-	Now, we have to encrypt the Nexus password as well. So first encrypt the admin password. So do
Mvn-ep admin “enter”
Still copy all what is inside the curly bracket and save it somewhere in your text edits.
{aptzIypiUZAHPix…..=} (copy this and save somewhere)
-	Now create a settings.xml file and vi into it. So do Vi settings.xml “enter”
Copy the content of this cook from the Drive and paste it here.
<?xml version……..
<…………..
<username>admin<username>
<password>(aptztypilUZAHPix…….=)
<……….
<………..
< url>http://13.235.132.119:8081/repository/maven-project/</url>
-	Now save and quite edc :wql “enter”
-	Now,more the file settings.xml to the file va./lib/Jenkins/m2.so do
Mv settings.xml/var/lib/Jenkins/.m2 “enter”
-	In the same way, move the settings security.xml to /liar/lib/Jenkins/.m2.so do
Mv settings-security.xml/var/lib/Jenkins/.m2 “enter”
-	Now Ls to check and confirm. So do
Ls/var/lib/Jenkins/.m2 “enter”
-	Then get into the folder by doing 
Cd/var/lib/Jenkins/.m2
-	Now change ownership of settings.xml and settings security.xml.rods
Chown jenjins:Jenkins settings.xml “enter”
-	Change ownership of settings-security.xml also. So do
Chown Jenkins:Jenkins settings-security.xml “enter”
-	Now finalli change the permission to do so
Chmod 755 settings.xml settings-security.xml “enter”. It will give the execution right.
o	Now go to the4 code base acces the code in the ceithub repository.
Then open the pom.xml file
Scroll down to line 32 under snapshotrepository
In line 32 replace the up there with Nexus private IP there in the URL there to appear as follows
<url>http://172.31.6.26:8081/repository/maven-snapshots/<url> (insect your Nexus private IP here).
Also in line 36, also replace the IP there with your Nexus private IP as well to appear as follow too
<url>http://172.31.6.26:8081/repository/maven-releases/<url> (Also insect your Nexus private IP here as well)
o	Now ensure you are inside the repository in the Terminal and commit the changes. So in that terminal,do
Git add. “enter”
Git commit in “nexus changes” enter
Git push “enter”
Now go run the “Build now” to test.
        Success
o	 Now, access the Nexus console. So go to Nexus UL
-	Then refresh the page and click on repository Icon at the top
-	Them click on “Browse” at the left
-	Then click on “maven-project”
As you see all those potates(these are the dependencies downloaded), there which have been downloaded and pushed to Nexus, click on the Holah that says COM
-	Then click on the folder whose name is example
-	Then click on the SNAPSHOT folder
-	You will then see your user file there appear as webapp-10.2021/22.031358-1.war
So this is the actual antifact that we created.
(D)	Deployment using Ansible.
•	Now go to your Jenkins-lever Terminal. We shall install and set up Ansible on this Jenkins-server. So run this command. 
Sudo amazon-linux-extras install ansible2-y ‘enter’







•	Now because of roof user. So do (Because unless you become a root user, you cannot create a new user). Sudo su
•	Now create a user called ansadmin. So do useradol ansadmin “enter”
•	Now setup a paaword for the user ansadmin. To set up this password, run this command. Password ansadmin “enter”
•	As you hit like, it gives you the prompt to enter the password so enter the password.ansadmin
•	Retype the password: ansadmin.
•	Now make sure that your pipeline script has this line of code in around line 13 either above tools or below tools environment.
Workspace=”${env.WORKSPACE}” workspace is the folder where all the configuration files and the pipeline is sound there which is on the O.S level. It is /var/hib/Jenkins/workspace/Airbnb-app deployment pipeline.
E) Create the Dev, stage and prod servers. So,
- go to EC2 instance page and click on “launch instance”
Name:server.            Summary.No of instances:3
O.S (AMI): Amazon linux
Instance type: t2-micro
Select your keypair
Network settings: click on “Edit”
Scroll down to click on “add security Group role”
Type.             Protocol.     Port range.        Source type
Custom TCP  TCP.          8080.                   Anywhere
 (As it is a tom cat application, the default port # is 8080. Note this put has nothing to do with the 8080 Jenkins. These instance are completely different from each other and from Jenkins).
-	Click on “Advance detail”
-	On user data selection
This is the link to fetch the user data, copy the installation script from this link
https://github.com/cvamsikrisham11/eagles-batch-devops-projects/blob/ansible-deployment-setup/userdata.sh
so, copy the user data script and paste in your user data box it start from
#!/bin/bash
……………
……………
……………
Sudo systemctl start tomcat (end here)
-	Then click on “laund instances”
-	Now go to the name of those instances
-	Click on each name to append the names to appear like this
Server-dev
Server- stage
Server- prod
•	 Now, go to the Jenkins terminal and become a root user. Sudo su. As we have already install ansible there, it is non able to execute from ansible commands now. So
While inside this Jenkins terminal, change the directory and get into the /etc/ansible/directory. So do cd/etc/ansible/ “enter”
•	Now, if you do ls “enter” you should see the hosts file amongst home other files
•	Now use nano to get into this hosts file, as we are going to edit it. So do nano host “enter”
•	Use the down arrow to come down to any chosen place, press “enter” to create some space.
Copy the 6th step and paste it under the inside this hosts file and edit it to become as follows
[dev].      Insert the private IP of server-dev
3.108.227.139	ansible-user=ansadmin ansible-password=ansadium
[stage] (insert the private IP of server-stage here)
172.31.90.32 ansible-user=ansadmin ansible-password=ansadmin
[prod] (insert the private IP of server-prod here)
172.31.85.89 ansible-user=ansadmin ansible-password=ansadium
- Exit from the nano editor by doing control + x
                                                        Thench type Y
                                                       Then press ‘enter’
-Now disable the host key settings in the ansible.cfg so that it will not be asking us to manually say “yes” to connect to those servers by Jenkins. So while inside  the /etc/ansible do vi ansible.cfg “enter”
- use host-key-checking to search for it. So to search it do :/host-key-checking “enter”
- it will directly take you to the host-key-checking
- un comment it, as it is currently commented. So uncomment it to appear as follows
Host-key-checking=false
-	Now save and quite ecs :wq! “enter”
So our ansible which is part of the Jenkins all nearby in this Jenkins server to grab the artifact and display it to those instances.
If you are using VPC peering, use private IPS
If you are using some external system, use public TPS
Now you can test it using the public IP: 8080 “enter”
                                                   IP:8080/Webapp”enter”
F) Slack Notification integration. To integrate stack on the pipeline
- Access the Jenkins Dashboard
- click on “New item”
Name; slack-practice-pipeline
Click on “pipeline” to select it
-then click on o.k
-on the ‘general’ page that pops up, scroll down to the script section
Click on the drop down on “try sample pipeline”
Then select’ hello world’
-click on save
-then click on ‘build now’ to ensure that its working
Now the plan is to integrate our pipeline or Jenkins environment with shack (which is completely outside of Jenkins environment) so it could be shack or google chart etc. How can we integrate such a thing easily.
-	We should always check or search if there is any of their plugins available. (If you are serach over google for Jenkins and slack integration, you will see that the name of it is ‘shack notification’ which is the plugin name.
-	So, go to the Jenkins dashboard and go to “Manage Jenkins”
-	Here, click on “Manage plugins”
Click on ‘avaailable’
-	In the filter box , type to search for slack
-	Check ‘slack notification’
-	Click on ‘install without restart’
  Success
-	Click on ‘go back to the top page’ to go back to the main dashboard
Now click on our’ slack-practice-pipeline’ that we created now
Duplicate the tap
-	In one key click on ‘configure’
-	Scroll down to our pipeline script or code of hello world
-	Now, click on pipeline syntax to open it ina  new tap
-	In the box, click inside to populate a list where you can now locate and see slack items that have been made available after we install the slack plugins.
No we need to create a new slack workspace or use an existing slack workspace that you plugs here.
Whenever you want to integrate google chat to Jenkins or slack to Jenkins or microscope teams to Jenkins, then you will need to go to the official documentation of that google chant and Jenkins or the other and film the process therein the documentation, Example; Jenkins to slack notification. Search it
•	Click on the link vansi sent to joint his slack channel. Team-develops.
•	You must be logged into slack first
https://devopsfullyou-r0x2686.slack.com/archives/co4CHFB2KBP
Now assume that we are the devops team. And we want to integrate this slack channel with our Jenkins. So it could be a google chart channel. All the same.
o	To integrfate any channel, once we are a member to that channel, click on the name of the channel on the left i.e # team-devop (click here). Click on this name
o	Then click on the drop down to populate a bax to see the details of that channel.
o	Locate ‘integration’ and click on it (because we want to integrate this slack channel to an application called Jenkins)
o	click on add on app under “apps”
o	search in the box for “Jenkins” and click on install beside Jenkins.
-	If it's not the first time you will see ‘view’ to click on it
o	 then click on ‘configuration’ under Jenkins logo
-	click now on ‘add to slack’
o	click now on the drop down to select ‘team DevOps’
o	then you click on ‘add Jenkins’ (1 Integration)
-	now duplicate the Jenkins tap
-	then go to the Jenkins dashboard
-	then go to the ‘manage Jenkins’
-	then click on ‘configure system’
-	then Scroll down all the way to the bottom to locate the slack session
-	workspace name; DevOps fully automated (walk space is a name that is generally on top and it is hosting the teams DevOps in it)
-	credentials: click on add
                    then click on ‘Jenkins’
on the box that comes off click on “kind” and select “secret text”
secret: go back to the slack app as screwed down to “ Tolken” copy the token in the box there and come past it in here.
-	ID: slack token
-	Description: stack-token
-	then click on “add”
-	now under “credentials” click on the drop down to select your “slack token” that is now there.
-	Now click on “save”
o	now go to the pipeline syntax and click on it
o	in the sample step search and click on “slack send: send slack message”
o	channel # teams-devops (make sure you put the # infront)
o	message: hello from Jenkins
o	then click on “generate pipeline script”
o	now copy the line of script that appear below or that got generated “slacksend channel:#team-devops!message: “hello from Jenkins”
o	now go through our slack-practice-pipeline and Scroll down to the script and paste it below echo.hello would”
o	then click on “save” go and run the pipeline now
                  FAIL
to fix this,
o	go to manage Jenkins
o	then go to “configure system”
o	then Scroll down all the way to slack
under workspace: instead of DevOps fully automated, add the teams sub domain name on it for it to appear as follows
workspace name: devopsfullyau-r0x2686
-	click now on “save”
-	then click on build now
o	now go to the slack team channel you will see that the message we put there Jenkins is able to send it to the team channel
o	now you can copy the slack notifications step from line 249 to line 257 in the Jenkins script and set it up in the Jenkins file.
Passing move effective data from Jenkins to slack. Using environment variables as seen in this lock notification section of the pipeline script which appears thus






o	now change the channel name from”mbandi-jenkins-cicd-pipeline-alert and insert ‘team-devops” there to become as follows slacksend channel: “#team-devops” message “hello”
slacksend channels: team-devops;//update and provide your channel name.
o	The message line in 254 has to do with default environmental variable. These appears as
Message: “*${currentbuild.currentresult}*job${env.JOB-NAME}build${env.BUILD-NUMBER}n more info at : ${env.BUILD-URL}”
So these environment variable values will be replaced during execution this is how we do to send the data to slack from Jenkins.
(So we install a slack notification token on HP Jenkins site, then on the slack side we create a token and paste this token in Jenkins so that Jenkins used this token to connect and access slack in order to push notification to slack)
(G) continuous monitoring with Prometheus and Grafana.
So we promethus and Grafana, We monitor both the OS and Jenkins.
Now let's set up Prometheus and grafana and install them in individual servers. So
-	click on “launch instance”
name: Prometheus-server
O.S: Amazon linux
Instance type: t2-micro
Keypair: select your keypair that you have access to it.
-	Put number on of promethus is 9090 so
-	Click on “edit” under “network settings”
-	Under normal security group rules, click on “add security group rule”
Type.              Protocol.  Port range.     Source type
Custom TCP      TCP.       9090.               Anywhere
-	Click inside “user data” under advanced detail. Use this link to get the user data to paste inside this user data box.
https://github.com/cvamsikrishna11/devops-fully-automated/blob/installations/prometheus-setup.sh
It is cloning a particular git branch that has to do with installation under it line 23-25. It is using the Prometheus.yaml file and Prometheus. Server In this repository to set up promethus and help to do the monitoring. So the user data will download it directly from the repo.
o	Copy the user data in this repo using the copy clipboard and paste it inside the user data box.
o	Now screw up and locate”IAM INSTANCE PROFILE”. We are going to attach a role here.
o	Click inside the box below “I am instance profile” an search for the role that we created called “Prometheus server role” and click on it.
(If you can attach any admin EC2 role that you have, it will be enough. It should have an EC2 read only access)
So that Prometheus should be able to discover those EC2 instances, using service discovery and collect the logs.
-	Now click on “launch instance” to create it
now access a Prometheus UI by. So in a new browser do,
IP of Prometheus server: 9090
44.211.170.128:9090
Prometheus UI comes up
Now look at the top and click on “status” then click on “target”.
Now keep this tap as it is and duplicate the tap.
Now click on “status” and then click on “configurations”
Now, scroll down to locate a section that has to do with ec2-sd-configs (which appears as follows)
                          Ec2-sd-configs:
                             -endpoint:
                           Region:us-east-1
                           Refresh-interval:1m
                             Port:9100
                            Filters: [ ]
(This is nothing but the service discovery. So it will go to the us-east-1 reopen and discover those EC2 which has the service discovery pot of 9100 open. Any instance that does not have that pot 9100 open, it will not discover it. So Node Ex puter runs on part 9100 and Node Exputer installed on it)
So to make prometheus discover any instance that you want in the US-East-1 reopen, Search as prod server, go to that instance, install node exporter in it and open put 9100 on it. So with that Prometheus service discovery would discover it and mark it up. So as we want to discover the prods server,
o	Click on “server prod”. But the ideal time to install the Node exporter in this instances is doing their creation, (So as to capture all the logs)
if it is a production server, it can also have proxy servers like Tom cat installed in the user data as mel.
o	Click now on “connect” to connect to this prod server
o	Now convert to a roof user. So do,
Sudo su “enter”
o	Now install git. So do,
Yum install git-y “enter”
o	Now clone this repository
Github.com/awanlubandi/eagles-batch-devops-projects
So copy its git URL and inside the prod-server, do
Git clone https://github.com/awanmbandi/easgles-batch-devops-projects.git “enter”
-	Do LS to see the file appears
                  Eagles-batch-devops-projects
-	Now cd into the file.so do
Cd eagles-batch-devops-project “enter”
-	Now do ls to see these file
    Deplo.yaml Jenkinsfile, pipeline-env-requirement imd pan.xml etc
Now, the promethues related installation files are not here because they are not in a default main branch.
-So switch or change  the branch to Prometheus and Grafana so do.
Git checkout Prometheus-and-grafana “enter”
-if you do LS now, you will see the Prometheus related installation files.
-now we shall install Node Exputer in this prod-server instances.
So do install the Node Exputer here, there is a file called install-node-exporter.sh
We have to execute this file to execute this shall file, do bash install-node-exputer.sh “enter” 








1) Create a GitHub Repository `Jenkins-CICD-Pipeline-Project` and push the code in this branch(main) to 
    your remote repository (your newly created repository). 
    - Go to GitHub (github.com)
    - Login to your GitHub Account
    - Create a Repository called "Jenkins-CICD-Project"
    - Clone the Repository in the "Repository" directory/folder in your local
    - Download the code in in this repository "Main branch": use this link [here](https://github.com/awanmbandi/eagles-batch-devops-projects.git)
    - Unzip the code/zipped file
    - Copy and Paste everything from the zipped file into the repository you cloned in your local
    - Add the code to git, commit and push it to your upstream branch "main or master"
    - Confirm that the code exist on GitHub

2) Jenkins/Maven/Ansible
    - Create an Amazon Linux 2 VM instance and call it "jenkins-maven-ansible"
    - Instance type: t2.medium
    - Security Group (Open): 8080, 9100 and 22 to 0.0.0.0/0
    - Key pair: Select or create a new keypair
    - User data (Copy the following user data): [link](https://github.com/awanmbandi/eagles-batch-devops-projects/blob/maven-nexus-sonarqube-jenkins-install/jenkins-install.sh)
    - Launch Instance

3) SonarQube
    - Create an Create an Ubuntu 18.04 VM instance and call it "SonarQube"
    - Instance type: t2.medium
    - Security Group (Open): 9000, 9100 and 22 to 0.0.0.0/0
    - Key pair: Select or create a new keypair
    - User data (Copy the following user data): [link](https://github.com/awanmbandi/eagles-batch-devops-projects/blob/maven-nexus-sonarqube-jenkins-install/sonarqube-install.sh)
    - Launch Instance

4) Nexus
    - Create an Amazon Linux 2 VM instance and call it "Nexus"
    - Instance type: t2.medium
    - Security Group (Open): 8081, 9100 and 22 to 0.0.0.0/0
    - Key pair: Select or create a new keypair
    - User data (Copy the following user data): [link](https://github.com/awanmbandi/eagles-batch-devops-projects/blob/maven-nexus-sonarqube-jenkins-install/nexus-install.sh)
    - Launch Instance

5) EC2 (Dev/Stage/Prod)
    - Create 3 Amazon Linux 2 VM instance and call them (Names: Dev-Env, Stage-Env and Prod-Env)
    - Instance type: t2.micro
    - Security Group (Open): 8080, 9100 and 22 to 0.0.0.0/0
    - Key pair: Select or create a new keypair

6) Prometheus
    - Create an Ubuntu 20.04 VM instance and call it "Prometheus"
    - Instance type: t2.micro
    - Security Group (Open): 9090 and 22 to 0.0.0.0/0
    - Key pair: Select or create a new keypair
    - Launch Instance

7) Grafana
    - Create an Ubuntu 20.04 VM instance and call it "Grafana"
    - Instance type: t2.micro
    - Security Group (Open): 3000 and 22 to 0.0.0.0/0
    - Key pair: Select or create a new keypair
    - Launch Instance

8) Slack 
    - Go to the bellow Workspace and create a Private Slack Channel and name it "yourfirstname-jenkins-cicd-pipeline-alerts"
    - Link: https://app.slack.com/client/T043JRQBB5L/C044F5PH3DE 

9) - ***Missing Notes here; Contact Kenneth for this. Thanks***
     
10) Configure Grafana
    - Login/SSH to your Grafana Server
    - Clone the following repository: [here](https://github.com/awanmbandi/eagles-batch-devops-projects.git)
    - Change directory to "eagles-batch-devops-projects"
    - Swtitch to the "prometheus-and-grafana" git branch 
    - Run: ls or ll  (to confirm you have the branch files)
    - Run: ./install-grafana.sh
    - Confirm the status shows "Active (running)"
    - Exit

11) Configure The "Node Exporter" accross the "Dev", "Stage" and "Prod" instances including your "Pipeline Infra"
    - Login/SSH into the "Dev-Env", "Stage-Env" and "Prod-Env" VM instance
    - Perform the following operations on all of them
    - Install git by running: sudo yum install git -y 
    - Clone the following repository: [link](https://github.com/awanmbandi/eagles-batch-devops-projects.git)
    - Change directory to "eagles-batch-devops-projects"
    - Swtitch to the "prometheus-and-grafana" git branch 
    - Run: ls or ll  (to confirm you have the branch files)
    - Run: ./install-node-exporter.sh
    - Confirm the status shows "Active (running)"
    - Access the Node Exporters running on port "9100", open your browser and run the below
        - Dev-EnvPublicIPaddress:9100   (Confirm this page is accessible)
        - Stage-EnvPublicIPaddress:9100   (Confirm this page is accessible)
        - Prod-EnvPublicIPaddress:9100   (Confirm this page is accessible)
    - Exit

12) Configure The "Node Exporter" on the "Jenkins-Maven-Ansible", "Nexus" and "SonarQube" instances 
    - Login/SSH into the "Jenkins-Maven-Ansible", "Nexus" and "SonarQube" VM instance
    - Perform the following operations on all of them
    - Install git by running: sudo yum install git -y    (The SonarQube server already has git)
    - Clone the following repository: [here](https://github.com/awanmbandi/eagles-batch-devops-projects.git)
    - Change directory to "eagles-batch-devops-projects"
    - Swtitch to the "prometheus-and-grafana" git branch 
    - Run: ls or ll  (to confirm you have the branch files including "install-node-exporter.sh")
    - Run: ./install-node-exporter.sh
    - Make sure the status shows "Active (running)"
    - Access the Node Exporters running on port "9100", open your browser and run the below
        - Jenkins-Maven-AnsiblePublicIPaddress:9100   (Confirm the pages are accessible)
        - NexusPublicIPaddress:9100   
        - SonarQubePublicIPaddress:9100   
    - Exit

13) Update the Prometheus config file and include all the IP Addresses of the Pipeline Instances that are 
    running the Node Exporter API. That'll include ("Dev", "Stage", "Prod", "Jenkins-Maven-Ansible", "Nexus" and "SonarQube")
    - SSH into the Prometheus instance either using your GitBash (Windows) or Terminal (macOS) or browser
    - Run the command: sudo vi /etc/prometheus/prometheus.yml
        - Navigate to "- targets: ['localhost:9090']" and add the "IPAddress:9100" for all the above Pipeline instances. Ecample "- targets: ['localhost:9090', 'DevIPAddress:9100', 'StageIPAddress:9100', 'ProdIPAddress:9100', 'Jenkins-Maven-AnsibleIPAddress:9100'] ETC..."
        - Save the Config File and Quit
    - Open a TAB on your choice browser
    - Copy the Prometheus PublicIP Addres and paste on the browser/tab with port 9100 e.g "PrometheusPublicIPAddres:9100"
        - Once you get to the Prometheus Dashboard Click on "Status" and Click on "Targets"
    - Confirm that Prometheus is able to reach everyone of your Nodes, do this by confirming the Status "UP" (green)
    - Done

14) Open a New Tab on your browser for Grafana also if you've not done so already. 
    - Copy your Grafana Instance Public IP and put on the browser with port 3000 e.g "GrafanaPublic:3000"
    - Once the UI Opens pass the following username and password
        - Username: admin
        - Password: admin
        - New Username: admin
        - New Password: admin
        - Save and Continue
    - Once you get into Grafana, follow the below steps to Import a Dashboard into Grafana to visualize your Infrastructure/App Metrics
        - Click on "Configuration/Settings" on your left
        - Click on "Data Sources"
        - Click on "Add Data Source"
        - Select Prometheus
        - Underneath HTTP URL: http://PrometheusPublicOrPrivateIPaddress:9090
        - Click on "SAVE and TEST"
    - Navigate to "Create" on your left (the `+` sign)
        - Click on "Import"
        - Copy the following link: https://grafana.com/grafana/dashboards/1860
        - Paste the above link where you have "Import Via Grafana.com"
        - Click on Load (The one right beside the link you just pasted)
        - Scrol down to "Prometheus" and select the "Data Source" you defined ealier which is "Prometheus"
        - CLICK on "Import"
    - Refresh your Grafana Dashbaord 
        - Click on the "Drop Down" for "Host" and select any of the "Instances(IP)"

15) Update Your Jenkins file with your Slack Channel Name
    - Go back to your local, open your "Jenkins-CICD-Project" repo/folder/directory on VSCODE
    - Open your "Jenkinsfile"
    - Update the slack channel name on line "97"
    - Change name from "jenkins-cicd-pipeline-alerts" to yours
    - Add the changes to git, commit and push to GitHub
    - Confirm the changes reflects on GitHub

16) Copy your Jenkins Public IP Address and paste on the browser = ExternalIP:8080
    - Login to your Jenkins instance using your Shell (GitBash or your Mac Terminal)
    - Copy the Path from the Jenkins UI to get the Administrator Password
        - Run: `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
        - Copy the password and login to Jenkins
    - Plugins: Choose Install Suggested Plugings 
    - Provide 
        - Username: admin
        - Password: admin
        - Name and Email can also be admin. You can use `admin` all through as we
    - Continue and Start using Jenkins

17) Once on the Jenkins Dashboard
    - Click on "Manage Jenkins"
    - Click on "Plugin Manager"
    - Click "Available"
    - Search and Install the following Plugings "Install Without Restart"
        - SonnarQube Scanner
        - Maven Integration
        - Pipeline Maven Integration
        - Maven Release Plug-In
        - Slack Notification
    - Install all plugings without restart 

18) Confirm and make test your installations/setups  
