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
                sh label: '', script: 'docker build -t 65.0.6.89:8084/spring_mongoapp:${BUILD_NUMBER} .'
            }
        }
        stage("Push_Image_Nexus") {
            steps {
                withCredentials([string(credentialsId: 'nexus', variable: 'Nexus_PWD')]) {
                sh label: '', script: 'docker login 65.0.6.89:8084 -u admin -p ${Nexus_PWD}'
                }
                sh label: '', script: 'docker push 65.0.6.89:8084/spring_mongoapp:${BUILD_NUMBER}'
            }
        }
        stage("deploy_To_Swarm") {
            steps {
                sshagent(['dockerswarm']){
                sh label: '', script: 'scp -o StrictHostKeyChecking=no docker-compose-swarm.yml ubuntu@52.66.109.113:'
                sh label: '', script: 'ssh -o StrictHostKeyChecking=no ubuntu@52.66.109.113 docker stack rm spring || true '
                sh label: '', script: 'ssh -o StrictHostKeyChecking=no ubuntu@52.66.109.113 docker stack deploy --compose-file docker-compose-swarm.yml spring'    
                }
                    
            }
        }
    }
}
