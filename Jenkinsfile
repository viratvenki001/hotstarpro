pipeline {
    agent any
     tools {
        nodejs 'nodejs'
    }

    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('gitclone') {
            steps {
                git 'https://github.com/jetty251/hotstart.HOT.git'
            }
        }
         stage('s3 backup') {
            steps {
                 sh 'aws s3 cp /var/lib/jenkins/workspace/hot.project/ s3://jetty-hotstar/jetty-hotstar2/ --recursive'
            }
         }
         stage('deploy to tomcat') {
            steps {
                 sh "sudo scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/hot.project/package.json package-lock.json root@16.171.59.245:/root/tomcat/webapps/"
            }
        }
         stage('build docker image') {
            steps {
                withDockerRegistry(credentialsId: 'docker-tocken', toolName: 'jetty45') {
                    sh "docker build -t jetty45/hotstarpro:v1 ." 
                }
            }
        }
         stage('push docker image') {
            steps {
                withDockerRegistry(credentialsId: 'docker-tocken', toolName: 'jetty45') {
                    sh "docker push jetty45/hotstarpro:v1" 
                }
            }
        }
         stage('deploy to container') {
            steps {
                withDockerRegistry(credentialsId: 'docker-tocken', toolName: 'jetty45') {
                    sh "docker run -d -p 3000:3000 jetty45/hotstarpro:v1" 
                }
            }
        }
    }
}

