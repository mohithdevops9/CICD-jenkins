# CICD-jenkins

Create a instance with t2.large and install the jenkins.
 sudo apt update
 sudo apt install docker.io
 sudo docker pull jenkins/jenkins
 sudo docker images
 sudo docker run -id --name jenkinscon -p 8080:8080 907bfc22c9fb
 sudo docker ps
 sudo docker exec ff5ade2beea2 cat /var/jenkins_home/secrets/initialAdminPassword

 >open brower :http://100.26.133.205:8080/ and paste the password.
 choose default plugins

 >Create a job
  name: CICD
  choose: pipeline
>Configuration
# stage-1 :Source code managament
 Pipeline > Pipeline Script
>pipeline syntax:
 Sample Step :git:Git
               Repository URL:https://github.com/mohithdevops9/hello_world_public_war.git
               Branch : main
               credentials:
                  Domain:Global credentials
                  Kind:username with password
                  Username:mohithdevops9
                  password:(generate the token from github and paste here)
                  ID: github-token
                  Description: githublogin then #add
             credentials: choose the added credentials.
Click on Generate pipeline script:
>git branch: 'main', credentialsId: 'github-token', url: 'https://github.com/mohithdevops9/hello_world_public_war.git'
>Go back to Pipeline Script
>pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
                git branch: 'main', credentialsId: 'github-token', url: 'https://github.com/mohithdevops9/hello_world_public_war.git'
            }
        }
    }
}

> cloned successfully to this location /var/jenkins_home/workspace/CICD.
# stage-1 completed.

# stage-2 : scanning the code for validation with help of sonarqube.

 Create a instance with t2.large and install the SonarQube
 sudo apt update
 sudo apt install docker.io
 sudo docker pull sonarqube
 sudo docker images
 sudo docker run -id --name sonar-con -p 9000:9000 -p 9092:9092 a02c33ab0411

 open browser : http://52.90.199.204:9000/ > this will open the sonarqube login page
 default user name and password is admin and admin
 then set new password > then it will take to home page of sonarqube
 
 >create a new project   >Project display name:first-scan
                         >Main branch name :main >click on next
                         Set up project for Clean as You Code:
                         >Choose the baseline for new code for this project> choose Use the global setting
                         > then create project.
                         >Analysis Method: click on With Jenkins
                                           >Select your DevOps platform> choose github
                                           >Create a Jenkinsfile >What option best describes your build?> choose MAVEN
                                           >Create a Jenkinsfile file in your repository and paste the following code:
  pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
                git branch: 'main', credentialsId: 'github-token', url: 'https://github.com/mohithdevops9/hello_world_public_war.git'
            }
        }
        stage('SonarQube Analysis') {
           steps{
           script {
              def mvn = tool 'maven';
              withSonarQubeEnv() {
             sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=first-scan -Dsonar.projectName='first-scan'"
             }
             }
             }
  }
    }
}                                         

>create the quality profiles with source code language type and quality gates with metrix conditions.
>integrate the quality gates with our project.

>Goback to jenkins and add the plugins SonarQube Scanner,Sonar Quality Gates,Quality Gates
>manage jenkins>System >add sonar qube
                       >name:
                       >sonar url:
                       >add credentials:
                        credentials:
                  Domain:Global credentials
                  Kind:screct text
                  Secret:(goto sonar website>account>security>generate token and paste in jenkins)
                  ID: sonar-token
                  Description: sonar-login then #add
             credentials: choose the added credentials and then click on save.
>manage jenkins >tools> add maven and then click on save # maven is adding because to generate binaries from SCM
>check the code is scanned successfully 
    
after these setting build the pipeline and observe the maven build binary present in workspace with path webapp/target/
                      
                        
 
