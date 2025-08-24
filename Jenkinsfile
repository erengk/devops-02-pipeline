pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK21'
    }

    stages {

       stage('SCM GitHub') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/erengk/devops-02-pipeline']])
            }
        }

        stage('Build Maven') {
            steps {
                sh 'mvn clean install'
            //    bat 'mvn clean install'
            }
        }

        stage('Test Maven') {
            steps {
                sh 'mvn test'
            //    bat 'mvn test'
            }
        }

        stage('Docker Image') {
            steps {
                sh 'docker build  -t gokhaneren/devops-application:latest   .'
            //    bat 'docker build  -t gokhaneren/devops-application:latest   .'
            }
        }
        stage('Docker Image to DockerHub') {
            steps{
                script{
                    withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
                        if(isUnix()){
                            sh 'echo docker login -u erengk   -p %dockerhub%'
                            sh 'echo docker push  gokhaneren/devops-application:latest'
                        }else{
                            bat 'docker login -u erengk -p %dockerhub%'
                            bat 'docker push gokhaneren/devops-application:latest'
                        }
                    }
                }
            }
        }
         stage('Deploy Kubernetes') {
                    steps {
                    script {
                            kubernetesDeploy (configs: 'deployment-service.yaml', kubeconfigId: 'kubernetes3')
                        }
                    }
                }


                stage('Docker Image to Clean') {
                    steps {

                           //  sh 'docker image prune -f'
                             bat 'docker image prune -f'

                    }
                }



            }

        }

        kubernetesDeploy configs: '/Users/erengk/.kube/config',
        kubeConfig: [path: ''],
        kubeconfigId: 'kubernetes',
        secretName: '',
        ssh: [sshCredentialsId: '*', sshServer: ''],
        textCredentials: [certificateAuthorityData: '',
        clientCertificateData: '',
        clientKeyData: '',
        serverUrl: 'https://']