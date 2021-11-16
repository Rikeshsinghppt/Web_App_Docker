DevOps Project:-

Deploy a application as a Docker Container.

Steps:-

Create Two Server using (EC2 Instaces)
1) Jenkins_Server
2) Docker_Deployment_Server

Connect both the servers with ssh .pem keys

1):- Configure Jenkins Server By installing:-
Jenkins and Docker on the Jenkins_Server
Cmd for that installations on jenkins_Server:-

#Install Jenkins:-
sudo apt update
sudo apt install openjdk-8-jdk
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins
sudo systemctl status jenkins
sudo systemctl start jenkins

#Install Docker:-
sudo apt install docker.io -y

#Add Jenkins user to docker
sudo usermod -aG docker jenkins

#Restart Jenkins
sudo systemctl restart jenkins 


2):- Configure Docker_Deployment_Server by installing:-
Docker on Docker_Deployment_Server

#Update package manager
sudo apt update

#Install Docker
sudo apt install docker.io -y

#Add ubuntu user to docker
sudo usermod -aG docker ubuntu

Now open Jenkins DashBoard in browser using Jenkins server public_id with jenkins port 
By default Jenkins Port number is 8080 (ex- 107.20.16.136:8080):-
create a id password for jenkins
Install default plugin in Jenkins
after all the jenkins setup create a job
 give the job name selete pipeline than click on OK

come to Pipeline write the script 

node {
    
    def buildNumber = BUILD_NUMBER
#cloning git repo    
    stage("Git Clone"){
        
        git url: 'https://github.com/Rikeshsinghppt/java-web-app-docker.git', branch:'master'
    }
#this stage ll clean maven package and ll give us artifact(Jar or war file)    
    
    stage("Maven Clean Package"){
        def mavenHOME= tool name: "Maven",type: "maven"
         sh "${mavenHOME}/bin/mvn clean package"
    }
#this stage ll build the docker image with the name you have given   
    stage("Build Docker Image"){
        sh "docker build -t rikeshimages/java-web-app-docker:${buildNumber} ."
    }
#this stage ll do login in ur dockerhub and push the docker image in your docker ragistory repo     
     stage("Docker Login And Push"){
         withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
            sh "docker login -u rikeshimages -p ${DockerHubPwd}"
          }
         sh "docker push rikeshimages/java-web-app-docker:${buildNumber}"
    }
}

Come on Jenkins dashboad=>manage Jenkins=> manage plugins
Install sshagent plugin in jenking using this plugin we ll connect 
deployment server and can execute some docker command.
