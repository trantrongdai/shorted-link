pipeline {
    agent any
    environment {     
        DOCKERHUB_CREDENTIALS = credentials('docker_cred')
    } 

    stages{
        stage("Build Maven") {
            steps{
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/trantrongdai/shorted-link-pro.git']])
            }
        }
        stage('Build docker image') {
            steps {
                script{
                    sh 'docker build -t trantrongdai/shorted-be -f /limits-service .'
                    sh 'docker build -t trantrongdai/shorted-fe -f /shorted-fe .'
                }
            }
        }
        stage('Login to Docker Hub') {         
            steps{
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            	echo 'Login Completed'                
             }           
        } 
        stage('Push image to hub'){
            steps {
                script{
                    sh 'docker push trantrongdai/shorted-be'
                    sh 'docker push trantrongdai/shorted-fe'
                }
            }
        }
        stage ('Deploy') {
            steps{
                sshagent(credentials : ['app-ssh']) {
                    sh 'ssh -o StrictHostKeyChecking=no ngan_jobpartner@34.87.97.87 uptime \
                    " sudo docker stop trantrongdai.shorted-be \
                    && sudo docker rm --force trantrongdai.shorted-be \
                    && sudo docker pull trantrongdai/shorted-be \
                    && sudo docker run -it -d -p 8080:8080 --name=trantrongdai.shorted-be trantrongdai/shorted-be"'
                }
            }
        }
    }
    post {
        always {
          sh 'docker logout'
        }
  }
}
