pipeline {
    agent any
    tools{
        maven 'maven'
    }
    stages{
        stage('Build Maven'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '457250a2-5054-43c6-b67b-60af5c1e641e', url: 'https://github.com/amitu2016/devops-automation.git']])
                bat 'mvn compile'
            }
        }
         stage('Unit Testing'){
            steps{
                bat 'mvn clean test'
            }
        }
        stage('SonarQube Analysis') {
            environment {
                SONAR_HOST_URL = 'http://localhost:9099' // Replace with your SonarQube URL
                SONAR_AUTH_TOKEN = credentials('sonarqube') // Store your token in Jenkins credentials
            }
            steps {
                bat 'mvn sonar:sonar -Dsonar.projectKey=sample_project -Dsonar.host.url=$SONAR_HOST_URL -Dsonar.login=$SONAR_AUTH_TOKEN'
            }
        }
        stage('Build docker image'){
            bat 'mvn clean package'
            steps{
                script{
                    bat 'docker build -t amitu2021/devops-integration .'
                }
            }
        }
          stage('Push image to Hub'){
            steps{
                script{
                   withCredentials([string(credentialsId: 'dcrpwd', variable: 'dcrpwd')]) {
                        bat "docker login -u amitu2021 -p ${dcrpwd}"
                }
                   bat 'docker push amitu2021/devops-integration'
                }
            }
        }
         stage('Deploy to k8s'){
            steps{
                script{
                   kubernetesDeploy (configs: 'deploymentservice.yaml', kubeconfigId: 'k8sconfigpwd')
                }
            }
        }
    }
}
