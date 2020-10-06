properties([[$class: 'JiraProjectProperty'], buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '5', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
pipeline {
    agent any
    tools {
    maven 'Maven_Home.3.6.3'
    }

    stages {
        stage("Scm_Checkout") {
            steps {
                git branch: 'stage', credentialsId: 'github_id', url: 'https://github.com/Dockernewravijigalooru/spring-boot-mongo-docker.git'
            }
        }
        stage("Maven_Buil_Artifact") {
            steps {
                sh label: '', script: 'mvn clean install'
            }
        }
        stage("Built_Image") {
            steps {
                sh label: '', script: 'docker build -t 13.233.122.83:8084/spring_mongoapp:${BUILD_NUMBER} .'
            }
        }
        stage("Push_Image_Nexus") {
            steps {
                withCredentials([string(credentialsId: 'nexus', variable: 'Nexus_PWD')]) {
                sh label: '', script: 'docker login 13.233.122.83:8084 -u admin -p ${Nexus_PWD}'
                }
                sh label: '', script: 'docker push 13.233.122.83:8084/spring_mongoapp:${BUILD_NUMBER}'
            }
        }
        stage("deploy_To_Swarm") {
            steps {
                sshagent(['dockerswarm']){
		withCredentials([string(credentialsId: 'nexus', variable: 'nexus_PWD')]) {
		sh label: '', script: 'ssh -o StrictHostKeyChecking=no ubuntu@13.233.49.242 docker login -u admin -p ${nexus_PWD } 13.233.122.83:8084'
		}
                sh label: '', script: 'scp -o StrictHostKeyChecking=no docker-compose-swarm.yml ubuntu@13.233.49.242:'
                sh label: '', script: 'ssh -o StrictHostKeyChecking=no ubuntu@13.233.49.242 docker stack rm spring || true '
                sh label: '', script: 'ssh -o StrictHostKeyChecking=no ubuntu@13.233.49.242 docker service create --name spring -d \n
-p 8080:8080 -e MONGO_DB_HOSTNAME=mongo -e MONGO_DB_USERNAME=devdb -e MONGO_DB_PASSWORD=devdb@1234 --network spring_mongonetwork --replicas 2 --with-registry-auth 13.233.122.83:8084/spring_mongoapp:${BUILD_NUMBER}'
		 sh label: '', script: 'ssh -o StrictHostKeyChecking=no ubuntu@13.233.49.242 docker service create --name mongo -d \n
-e MONGO_INITDB_ROOT_USERNAME=devdb MONGO_INITDB_ROOT_PASSWORD=devdb@1234 --network spring_mongonetwork -v mongovolume:/data/db mongo   
                }
                    
            }
        }
    }
}
