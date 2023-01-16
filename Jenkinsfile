pipeline {
    agent any
    environment {
        dockerImage = ''
    }
    stages {
        stage('poll scm') {
            steps {
                git credentialsId: 'git-token', url: 'git@github.com:kantakumari/Devops-advanced-assessment.git'
            }
        }
        stage('npm install'){
            steps {
                sh 'npm install'
            }
        }
        stage('npm test'){
            steps{
                echo 'running test'
            }
        }
        stage('sonarqube'){
            steps {
                sh ' sonar-scanner \
                        -Dsonar.projectKey=node-app \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=http://20.185.60.222:9000 \
                        -Dsonar.login=sqp_f130e393417d31b890a9b52ee3e7c4d41be8926e '
            }
        }
        stage('docker build') {
            steps{
                script{
                    dockerImage = docker.build("kkanta2609/node-app")
                }
            }
        }
        stage('docker push') {
            
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-hub', url: "") {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('install docker'){
            steps{
                ansiblePlaybook credentialsId: 'ansible-key', disableHostKeyChecking: true, inventory: 'ansible/dev.inv', playbook: 'ansible/docker.yaml'
            }
        }
        stage('deploy image'){
            steps{
                ansiblePlaybook credentialsId: 'ansible-key', disableHostKeyChecking: true, inventory: 'ansible/dev.inv', playbook: 'ansible/deploy-image.yaml'
            }
        }
    }
}
