pipeline {
    agent any 
    tools {
    maven 'Maven_Home.3.6.3'
    }

    stages {
        stage("scm_chweckout") {
            steps {
                git branch: 'stage', credentialsId: 'github_id', url: 'https://github.com/Dockernewravijigalooru/spring-boot-mongo-docker.git'
            }
        }
        stage("Build_artifact") {
            steps {
                sh label: '', script: 'mvn clean install' 
            }
        }
        stage("Build_Image") {
            steps {
                sh label: '', script: 'docker build -t amruth0005/spring_boot_application:${BUILD_NUMBER} .'
            }
        }
        stage("Push_Image_To_Dockerhub") {
            steps {
                withCredentials([string(credentialsId: 'amr_hub', variable: 'docherhub_PWD')]) {
                sh label: '', script: 'docker login -u amruth0005 -p ${docherhub_PWD}'
                }
                sh label: '', script: 'docker push amruth0005/spring_boot_application:${BUILD_NUMBER}'
            }
        }
    }
}
