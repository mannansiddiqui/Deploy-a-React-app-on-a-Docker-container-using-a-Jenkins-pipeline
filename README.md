# Deploy React app on Docker container via Jenkins pipeline

#### Description:

- Launch 2 EC2 server, 1st for Jenkins and 2nd for App deployment.
- On 1st server install and configure Jenkins
- On 2nd server, install Docker and dependencies to create React project.
- Create a React project.
- Upload this react project in GitHub.
- Make a CI/CD flow to deploy the code on Docker container via Jenkins.

#### Step-1: Launch 2 EC2 server, 1st for Jenkins and 2nd for App deployment

Firstly, log in to the AWS management console.

![Img-1](https://user-images.githubusercontent.com/74168188/178555843-f062573f-166c-4b06-b947-d2d11da46507.png)

After login, navigate to the search bar, type EC2, and select EC2

![Img-2](https://user-images.githubusercontent.com/74168188/178555883-5e169bfd-d205-4b99-9992-8dca7e957ff2.png)

Now, the EC2 dashboard will appear. Click Instances

![Img-3](https://user-images.githubusercontent.com/74168188/178555921-6213742a-726c-4532-923e-3edc4c4d1413.png)

Click the Launch instances button.

![Img-4](https://user-images.githubusercontent.com/74168188/178555939-529e74f0-6ece-43dc-aa1c-98d6b7f2deda.png)

To launch an EC2 instance, a few details are required, i.e., instance name, OS image (AMI), instance type, etc.

![5](https://user-images.githubusercontent.com/74168188/181441501-ce1a5d28-3114-4756-81a6-d45c6f8f4ebf.png)
![Img-6](https://user-images.githubusercontent.com/74168188/178556050-f90b180a-0dca-48fb-b30b-8365f9ac8f28.png)
![Img-7](https://user-images.githubusercontent.com/74168188/178556073-b0a18233-2e38-4dbd-926d-f7e411f7fa06.png)

Select a key pair to attach with the instance to log in with that key. If you do not have key pair, then create a new key pair by clicking Create a new key pair. Moreover, according to usage, download key pair in .pem or .ppk format.

![8](https://user-images.githubusercontent.com/74168188/179713553-227871ba-db62-496f-b361-2cf1ef3ff50e.png)

Now, to allow traffic from the internet, we need to create a rule in network settings.

![9](https://user-images.githubusercontent.com/74168188/179716719-a040568f-8b89-43df-817c-f39b35b19337.png)

We are good to go to launch an instance for this. Click the Launch instance button. If everything is correctly set up, you will find a success message on the screen. Click on the instance id to navigate to the EC2 dashboard.

![Img-11](https://user-images.githubusercontent.com/74168188/178556301-ac2e5bdd-7efa-4ad4-99d9-b669e599eefd.png)

Similarly, create another EC2 instance for app development.

#### Step-2: On 1st server install and configure Jenkins

Now, take the public IP from the EC2 dashboard and use it to login inside the instance using ssh. First move to the directory where the key is downloaded. In my case it's in downloads directory.

![12](https://user-images.githubusercontent.com/74168188/181449836-51c09040-e9b6-4d04-be5e-ac79e30fd525.png)

After logging in, first update local package index using ```sudo apt update```

![13](https://user-images.githubusercontent.com/74168188/181450073-47a9c873-c066-4a40-a426-ca5d1afac2ac.png)
![14](https://user-images.githubusercontent.com/74168188/181450200-a5e2c56b-3e89-4a04-b10d-648c5ead66c9.png)

Jenkins require jdk to run so let's install jdk using ```sudo apt install default-jdk -y```

![15](https://user-images.githubusercontent.com/74168188/181450766-ac29f9de-47c5-4bb9-a82b-bdb490df693a.png)
![16](https://user-images.githubusercontent.com/74168188/181450818-852df5e0-b158-470a-afc2-b85297d079ac.png)

To check jdk is successfully installed or not use ```java -version```

![17](https://user-images.githubusercontent.com/74168188/181451166-72af8b22-9c0d-4cd9-8df5-b6c4aa791ae9.png)

Now to install jenkins, follow below steps these are provided on jenkins official website also:

1. Add the key to your system using:
```
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
    /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

2. Then add a Jenkins apt repository entry:
```
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null
```

3. Update your local package index, then finally install Jenkins:
```
sudo apt-get update
  sudo apt-get install fontconfig openjdk-11-jre
  sudo apt-get install jenkins
```

![18](https://user-images.githubusercontent.com/74168188/181455964-6dd2eedc-e85d-45ad-9fce-c184784e6c6e.png)
![19](https://user-images.githubusercontent.com/74168188/181455980-8dc1ef3f-a361-4241-bae3-ff7020c35ca3.png)

To check jenkins is successfully installed or not use ```jenkins --version```

![20](https://user-images.githubusercontent.com/74168188/181456518-9104c47e-7390-4705-941b-7e8e4adf7b08.png)

Now, check the status of jenkins service using ```sudo systemctl status jenkins```

![21](https://user-images.githubusercontent.com/74168188/181457434-c493a5e4-5b07-448e-bdea-3b9ca41f8707.png)

As jenkins, by default runs on port number 8080 so we need to create firewall rule to enable port 8080. So that jenkins can be accessed using **INSTANCE_PUBLIC_IP:8080**

For this click **Security** as show in screenhot below then on secuity group that start from **sg-......**

![22](https://user-images.githubusercontent.com/74168188/181459864-1875260a-7227-467a-bfba-3e11c54b2aa4.png)

Here, click **Edit inbound rules** then on **Add rule**

![23](https://user-images.githubusercontent.com/74168188/181460272-a2799141-ab15-4855-9e81-588e3f20ac2b.png)

add rule for 8080 port from anywhere i.e. 0.0.0.0/0 then click **Save rules**

![24](https://user-images.githubusercontent.com/74168188/181460292-a77b09c4-888a-40b9-9663-826859ca86d3.png)


Now try to hit ```<INSTANCE_PUBLIC_IP>:8080```

![25](https://user-images.githubusercontent.com/74168188/181475134-e84bd05b-f0cb-49fc-b725-96e9334738cb.png)

As stated in above screenshot, to login to jenkins dashboard we have to provide admin password which is stored in **/var/lib/jenkins/secrets/initialAdminPassword** to see password use ```sudo cat /var/lib/jenkins/secrets/initialAdminPassword```

![26](https://user-images.githubusercontent.com/74168188/181476533-8bf12533-29bf-447c-ac96-0227f436fbf1.png)

Now, its asking to install plugins but we don't want to install so click on cancel

![27](https://user-images.githubusercontent.com/74168188/181477176-a7723ced-6383-4c0f-ae81-4cd60cce57e1.png)
![28](https://user-images.githubusercontent.com/74168188/181477283-5a102655-29f7-4f26-aad6-c3b55bcba231.png)

then click **start using jenkins**

![29](https://user-images.githubusercontent.com/74168188/181477671-18ea4985-cbfa-41fa-9bdf-5c0070e9128a.png)

As we have cancelled to install jenkins plugins so it also cancelled setting admin password. So we need to set admin password else if we logout we won't be able to login again.

So let's create/change admin password. For this click **Manage Jenkins** then in Security section click **Manage Users**

![30](https://user-images.githubusercontent.com/74168188/181479379-65711bc8-3d42-49fe-8d51-f9c409cb9a6f.png)

Now click on setting button of admin user

![31](https://user-images.githubusercontent.com/74168188/181479581-63f91d1b-5625-417c-8131-0aa1f3b3e1ef.png)

Scroll down and look for password section then enter password that you want and click **Save**

![32](https://user-images.githubusercontent.com/74168188/181479969-11213b98-76a4-4076-9e61-9c0eb23a5cc0.png)

Now again search for **INSTANCE_PUBLIC_IP:8080** form any browser and enter username and password

![33](https://user-images.githubusercontent.com/74168188/181480672-b72c5430-1905-400c-b796-4d13a79af5af.png)
![34](https://user-images.githubusercontent.com/74168188/181480741-3e7082bd-dd08-4bfe-be74-8ff12bc2d96f.png)

Now let's quickly configure 2nd instance as jenkins worker

Again we have to install jdk in worker node also. For this, first update local package index using ```sudo apt update``` then install jdk using ```sudo apt install default-jdk -y```

![35](https://user-images.githubusercontent.com/74168188/181504654-1a71e746-5316-4a6e-9074-2ac9ab6dac29.png)
![36](https://user-images.githubusercontent.com/74168188/181504722-ae88e8ef-7ffd-4e3f-a67f-657f43b48c0c.png)
![37](https://user-images.githubusercontent.com/74168188/181505121-5d5a0a53-77ba-4ad4-aae2-ec75fec57297.png)
![38](https://user-images.githubusercontent.com/74168188/181505133-53a6230f-9bf8-4799-af59-cb6de7835dc9.png)

As this instance is linux type so to work as worker node we need to launch agents over SSH. For this we need to install **SSH Build Agents** plugin in jenkins master. For this click manage jenkins

![39](https://user-images.githubusercontent.com/74168188/181510664-45a5885c-2140-4fa3-b395-5dd000d3e814.png)

In this page click **Manage Plugins**

![40](https://user-images.githubusercontent.com/74168188/181511041-31a701c0-2f2f-4542-a59d-98e6e28eed1c.png)

In available search **SSH Build Agents** and select then click **Download now and install after restart**

![41](https://user-images.githubusercontent.com/74168188/181512277-bb55f41f-c723-405d-970c-44f066d2a1e3.png)
![42](https://user-images.githubusercontent.com/74168188/181512423-5d3c1123-cdb2-4ec4-ae7a-ad08c0e3dc77.png)

Now we are good to go to connect worker node with master node.

Now, head to the jenkins dashboard. Click **Manage Jenkins** then click **Manage Nodes and Clouds**

![43](https://user-images.githubusercontent.com/74168188/181515177-eb22f77f-d36d-4eee-80ba-4bc0f0cd84ec.png)
![44](https://user-images.githubusercontent.com/74168188/181515250-b0536d45-d4cb-4bcc-a3bb-59281fc6e019.png)

Click **New Node**

![45](https://user-images.githubusercontent.com/74168188/181515327-bd1e2dd0-aff7-4772-9a04-858e38af8c0b.png)

Now, enter node name and select **Permanent Agent** then click **create**

![46](https://user-images.githubusercontent.com/74168188/181515396-59e2d518-c0f3-4b48-8476-b6b03d807689.png)

Fill few details like **No. of executors**, **Remote root directory**, **Usage**, **Launch method**, **Host**, **Credentials**, **Host Key Verification Strategy** are required.

![47](https://user-images.githubusercontent.com/74168188/181517007-088dfa50-f1c1-404a-b577-7c680bedfdb8.png)
![48](https://user-images.githubusercontent.com/74168188/182531157-fb79d3c1-3295-4d49-8baf-e7b3ad0560ab.png)

To connect this worker node to master node jenkins need to install agent for this jenkins requires username and private key of worker node.

![49](https://user-images.githubusercontent.com/74168188/181517248-a5d96fe3-d1ef-4615-a682-a194c09b75d3.png)
![50](https://user-images.githubusercontent.com/74168188/181517267-86aa9552-d7c9-4060-8542-7301db4e2aa7.png)
![51](https://user-images.githubusercontent.com/74168188/181517286-30eb4e93-215e-428c-b49a-e0d313270944.png)

Then finally click save. Now navigate to **Manage Jenkins** then **Manage Nodes and Clouds**.

![52](https://user-images.githubusercontent.com/74168188/182531966-2d972773-0ba1-4f23-8c59-7cf1bcbb6f64.png)

As you can see worker node is successfully connected to master node.

#### Step-3: On 2nd server, install Docker and dependencies to create React project

First login inside EC2 instance using ```sudo ssh -i <AWS_SECRET_KEY> ubuntu@<INSTANCE_PUBLIC_IP>```

![53](https://user-images.githubusercontent.com/74168188/182383812-c4c0f4d6-33bb-457a-b46f-0b3277cb2a66.png)
![54](https://user-images.githubusercontent.com/74168188/182383838-0978d82a-e7a9-4ebc-bade-3f0f740b026d.png)

To install Docker we will follow Docker official website **https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository**

So, first update local package index using ```sudo apt-get update```

![55](https://user-images.githubusercontent.com/74168188/182551077-6ae298c4-3cc2-4dec-9621-88ce0994adb6.png)

then install few packages using ```sudo apt-get install ca-certificates curl gnupg lsb-release```

![56](https://user-images.githubusercontent.com/74168188/182551250-55316bf8-c73b-4802-865a-ffe55ccc90c5.png)

Now, Add Docker’s official GPG key:
```
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
![57](https://user-images.githubusercontent.com/74168188/182551444-b03041c9-81a2-4d85-99f2-e8c0b9d0c77c.png)

Now, set up the repository:
```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
![58](https://user-images.githubusercontent.com/74168188/182551565-9088f58c-b5ce-4a2e-8d48-ca14bb05a1c6.png)

Finally update local package index and then install Docker:
```
sudo apt-get update
sudo apt-get install docker-ce -y
```
![59](https://user-images.githubusercontent.com/74168188/182551700-e8923f68-2308-4e3e-9011-bc83f736c3b1.png)
![60](https://user-images.githubusercontent.com/74168188/182552006-e03e436f-d116-4215-be7b-980fd0a68dd6.png)
![61](https://user-images.githubusercontent.com/74168188/182552108-3a3e3817-8ade-4e84-95ea-68e145ab61ff.png)

To check docker is successfully installed or not use ```sudo docker -v```

![62](https://user-images.githubusercontent.com/74168188/182552263-87cab382-42a3-4066-9fa3-2059e03f9ff2.png)

Now time to install to install nodejs. npm comes with nodejs bundled. To install nodejs, use below commands:
```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
```

![63](https://user-images.githubusercontent.com/74168188/182552418-df04c0bd-fa99-4eca-a7c9-cf93ccad786e.png)
![64](https://user-images.githubusercontent.com/74168188/182552607-22f31ff0-8af0-450f-bb8c-80fd3a5cacec.png)

To check nodejs is successfully installed or not use ```node -v```. Also, npm and npx is already installed. Use ```npm -v``` and ```npx -v``` to check version of npm and npx

![65](https://user-images.githubusercontent.com/74168188/182552742-bfb4c553-ad8f-43b1-bab5-cd80b43343af.png)

#### Step-4: Create a React project

To create react project we use yarn instead of npx. For creating react project use below commands:

```
sudo npm install -g yarn
sudo yarn global add create-react-app
sudo create-react-app <APP_NAME>
```
![66](https://user-images.githubusercontent.com/74168188/182571160-1e49ca4c-7974-496f-99d9-ff7418af3a7c.png)
![67](https://user-images.githubusercontent.com/74168188/182571291-935546ed-9c3b-4744-a4e5-4c0d998618c0.png)

#### Step-5: Upload this react project in GitHub

React project is successfully created now time to upload this project to GitHub. For this create an empty repo in GitHub.

![68](https://user-images.githubusercontent.com/74168188/182574605-33ab480b-f2f5-41d4-979f-62351fce3bec.png)

Now copy the repo url and clone in from worker node using ```https://github.com/mannansiddiqui/Deploy-React-app-on-Docker-container-using-jenkins-pipeline.git```.

![69](https://user-images.githubusercontent.com/74168188/182574864-e793b3c3-0673-43c0-9e59-1bae642e079c.png)

Now move/copy all files/folders from react project directory i.e. **testapp** to repo directory i.e. **Deploy-React-app-on-Docker-container-using-jenkins-pipeline** using ```cp -r testapp/* Deploy-React-app-on-Docker-container-using-jenkins-pipeline/```

![70](https://user-images.githubusercontent.com/74168188/182576063-a3938410-a3a1-479d-81b9-88eaa407eaf8.png)

Now from repo directory i.e. **Deploy-React-app-on-Docker-container-using-jenkins-pipeline** run ```git status```.

![71](https://user-images.githubusercontent.com/74168188/182576742-64432641-6ef7-4cb6-bf8d-3631aa218e54.png)

It shows all files untracked. To keep all files tracked by git use ```git add .```

![72](https://user-images.githubusercontent.com/74168188/182577755-d5c135eb-7812-48a0-a5da-12bd64303a6e.png)

Now again run ```git status``` to check status

![73](https://user-images.githubusercontent.com/74168188/182580079-e3af845c-ad8c-47fa-b6ad-e59a4585d249.png)
![74](https://user-images.githubusercontent.com/74168188/182578998-363c36fa-6423-4a5f-8d4c-5d98a46657f7.png)

As you can see now all files/folders are tracked by git. Now we have to commit all files/folders then push it to GitHub after providing GitHub credentials.

For commit use ```git commit -m "First Commit" .```

![75](https://user-images.githubusercontent.com/74168188/182581200-8d100889-9631-4efa-80e2-62fe8f0eb698.png)
![76](https://user-images.githubusercontent.com/74168188/182579598-1d84f443-a506-403e-89b1-1d2bd1f0cc6c.png)

Now time to push to GitHub using ```git push```

![77](https://user-images.githubusercontent.com/74168188/182581824-9fc405cd-2846-4293-83bc-3fcb2b05207b.png)
![78](https://user-images.githubusercontent.com/74168188/182582010-2841c471-8658-4e2f-b9e0-da6dc23018ef.png)

Files/Folders are successfully pushed to GitHub repo.

#### Step-6: Make a CI/CD flow to deploy the code on Docker container via Jenkins

Now we need to create a pipeline for next steps. But before this we have to install some plugins. First we require a **Pipeline** plugin to create a pipeline. Second we require **GitHub** plugin so that jenkins can interact with GitHub to download repo and many more things.

To install plugins navigate to **Manage Jenkins** then **Manage Plugins** and in **Available** search for **Pipeline** plugin and **GitHub** plugin then select and click on **Download now and install after restart** also don't forget to click on **restart after download completes**.

![79](https://user-images.githubusercontent.com/74168188/181734347-2adb6040-bba8-453e-a59a-5276f40b4d23.png)
![80](https://user-images.githubusercontent.com/74168188/181734362-9f7caed2-8502-4542-9419-4dd460378ab7.png)

![81](https://user-images.githubusercontent.com/74168188/181734395-d9c499fe-258d-44b9-85ac-c70f92bee33e.png)
![82](https://user-images.githubusercontent.com/74168188/181734406-ac191322-5504-499e-a644-7c76b2886c1c.png)

After restart you will get jenkins dashboard

![83](https://user-images.githubusercontent.com/74168188/181736072-d4e7c06b-5e3f-4dac-90e4-f1d6974d94f4.png)

Time to create a job. Enter job name and select **Pipeline** then click on **OK**.

![84](https://user-images.githubusercontent.com/74168188/182583596-b7325ddd-640b-4d63-b2a9-26583bf56a93.png)

We want as developer commit and push code to GitHub this job trigger and deploy the new code. For this we have to enable webhook inside GitHub repo.

First click on **settings** of GitHub repo

![85](https://user-images.githubusercontent.com/74168188/182585546-abc518a5-e309-482b-b72f-60d9974f64a8.png)

Now click on **Webhooks**

![86](https://user-images.githubusercontent.com/74168188/182585646-1b11992c-9292-4a8d-ad77-fa3e65b24f18.png)

Now click on **Add Webhook** it will ask your GitHub password

![87](https://user-images.githubusercontent.com/74168188/182586123-2511bcd3-2f5b-4147-880f-fdc7016da67d.png)

Now provide details of jenkins master i.e. **Payload URL** ```http://<INSTANCE_PUBLIC_IP>:8080/github-webhook/``` and select Content type **application/json** lastly select **Just the push event** it means as the push event occurs in this repo webhook will trigger the jenkins job so click **Add webhook**.

![88](https://user-images.githubusercontent.com/74168188/182586505-8fd130d1-36ef-4864-b6f6-e24469e03e99.png)

Moving back to the job. In **Build Triggers** select **GitHub hook trigger for GITScm polling** this means that trigger this job as the event occurs on repo.

![89](https://user-images.githubusercontent.com/74168188/182586881-e25a5463-8f04-4db6-96f5-a01569e04986.png)

Time to write pipeline script.

But before this we have to create a Dockerfile push it to GitHub repo so that when developer pushes the code job will trigger and GitHub repo files will be downloaded in worker node and this Dockerfile will be used by the Jenkins pipeline to build a new Docker image so that from this image pipeline can launch a container and deploy react app in it.

Here, I directly created a new file named Dockerfile in GitHub and write below content in this file then click on **Commit new file**:
```
FROM nginx:latest
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
RUN apt-get install -y nodejs
COPY build/ /usr/share/nginx/html/
```
![90](https://user-images.githubusercontent.com/74168188/182778690-c9b56cd9-7e39-4fc0-ae85-41484d19cbb0.png)

Now write the Groovy script to deploy react app on Docker container and click on **Save**
```
pipeline {
    
    agent { label 'Slave-2' }

    stages {
        
        stage('GitHub') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/mannansiddiqui/Deploy-React-app-on-Docker-container-using-jenkins-pipeline.git']]])
            }
        }
        
        stage('Build') {
            steps {
                sh 'sudo npm run build'
            }
        }
        
        stage('Deploy React App') {
            steps {
                sh 'sudo docker build -t mannansiddiqui/nginx-react-app:$BUILD_ID .'
                sh 'sudo docker container rm -f $(sudo docker container ls -laq)'
                sh 'sudo docker container run -dit -p 80:80 mannansiddiqui/nginx-react-app:$BUILD_ID'
                sh 'sudo docker container ls -a'
            }
        }
        
    }
}
```

![91](https://user-images.githubusercontent.com/74168188/182778846-81595c1b-ff89-413e-b23a-c0033ca17442.png)
![92](https://user-images.githubusercontent.com/74168188/182778864-96e7457b-699f-4281-8ece-4b828fa26711.png)

Lastly we have to create a firewall rule to allow port no. 80 of worker node.

![93](https://user-images.githubusercontent.com/74168188/182779347-949f48a9-b0ff-49a0-8d63-c14e4e0adb16.png)

Now time to hit worker node <INSTANCE_PUBLIC_IP>.

![94](https://user-images.githubusercontent.com/74168188/182779558-a820baaf-5632-45db-8929-9ac5cbd3164b.png)

We are able to see React app.

### Thank you...
