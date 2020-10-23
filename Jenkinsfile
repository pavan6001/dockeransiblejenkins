pipeline{
    agent any
    tools {
  maven 'MAVEN'
    }
    environment {
      DOCKER_TAG = getVersion()
    }

    stages{
        stage('SCM'){
            steps{
                git 'https://github.com/pavan6001/dockeransiblejenkins.git'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package" 
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t pavan6001/javaapp:${DOCKER_TAG} " 
            }
        }
        
         stage('DockerHub Push'){
            steps{
                    withCredentials([string(credentialsId: 'Docker_Hub', variable: 'DockerHubPwd')]) {
                         sh "docker login -u pavan6001 -p ${DockerHubPwd}"
                    }
                sh "docker push pavan6001/javaapp:${DOCKER_TAG} " 
            }
        }
        
        stage('Docker Deploy'){
            steps{
                ansiblePlaybook credentialsId: 'Dev-Server',disableHostKeyChecking: true, extras: "-e 'DOCKER_TAG=${DOCKER_TAG}", installation: 'Ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
        
    }

}

def getVersion(){
    def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
