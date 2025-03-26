#Tested integration of GitHub and Jenkins
based on the: 

## Steps:
### 1. Run locally Jenkins after installation:
```bash
java -jar //path_to_jar --httpPort=8089
```
### 2. Redirect traffic from publicweb, to localhost
#### Using Ngrok
https://dashboard.ngrok.com/get-started/setup/linux - configure
#### activate
```bash
ngrok http http://localhost:8089 <- has to match
```

```bash
                                                                                                                                     
Session Status                online                                                                                                 
Account                       Adrian (Plan: Free)                                                                                    
Version                       3.22.0                                                                                                 
Region                        Europe (eu)                                                                                            
Latency                       30ms                                                                                                   
Web Interface                 http://127.0.0.1:4040                                                                                  
Forwarding                    https://3b44-2a02-8108-4581-c900-5656-e33d-d274-98dd.ngrok-free.app -> http://localhost:8089           
                                                                                                                                     
Connections                   ttl     opn     rt1     rt5     p50     p90                                                            
                              2       0       0.00    0.00    30.47   30.66                                                          
                                                                                                                                     
HTTP Requests                                                                                                                        
-------------                                                                                                                        
                                                                                                                                     
17:57:26.445 CET POST /github-webhook/          200 OK                                                                               
17:56:18.911 CET POST /github-webhook/          200 OK   
```
### 3. Create a new Github repository and make a webhook
repo/repo-settings/webhook -> add ngrok https

### 4. Create Jenkinsfile in Github
```jenkins
pipeline {
    agent any
    
    tools {nodejs "node"}
    
    stages {
        stage('Cloning Git') {
            steps {
                git 'https://github.com/0110lekniw/mocha_testing'
            }
        }
        stage('Install Dependencies'){
            steps {
                script{
                    docker.image('node:16-alpine').inside {
                            sh 'npm install'
                        }
                }
            }
        }
        stage ('Test') {
            script{
                    docker.image('node:16-alpine').inside {
                            sh 'npm test'
                        }
                }
        }
    }
}
```
### 5. Connect jenkins-git with ssh
#### Jenkins bash
```bash
sudo su -s /bin/bash jenkins
```
#### Gen Key
```bash
ssh-keygen
```
### 6. Add ssh-key to github
```bash
cat /path/to/key (PUBLIC NOT PRIVATE!)
```
Github user-settings -> add new ssh

### 7. Create Pipeline
add github repo and
choose: GitHub hook trigger for GITScm polling

### 8. Push changes to Git and check the pipeline

Works!
