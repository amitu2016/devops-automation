pipeline {
    agent any
    tools{
        maven 'maven'
    }
    stages{
        stage('Build Maven'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '457250a2-5054-43c6-b67b-60af5c1e641e', url: 'https://github.com/amitu2016/devops-automation.git']])
                bat 'mvn clean install'
            }
        }
        stage('Build docker image'){
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
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'k8sconfigpwd')
                }
            }
        }
    }
}
