pipeline{
    agent any
    tools {
      maven 'M2_HOME'
    }
    // environment {
    //   DOCKER_TAG = getVersion()
    // }
    stages{
        stage('Clean Workspace'){
            steps{
              cleanWs()   
            }
        }
        stage('SCM'){
            steps{
                git branch: 'main', url: 'https://github.com/arikbidny/java-devops-ansible-docker.git'
                // git 'https://github.com/arikbidny/java-devops-ansible-docker.git'
            }
        }
        stage('Maven Build'){
            steps{
                sh "mvn clean install"
            }
        }
        stage('Docker Build'){
            steps{
                script{
                    DOCKER_TAG = getVersion()
                }
                sh "docker build . -t arikbi/javaansibledocker:$DOCKER_TAG"
            }
        }
        stage('DockerHub Push'){
            steps{
                script{
                    DOCKER_TAG = getVersion()
                }
                withCredentials([string(credentialsId: 'docker-hub-arikbi', variable: 'dockerHubPwd')]) {
                    sh "docker login -u arikbi -p ${dockerHubPwd}"
                }
                sh "docker push arikbi/javaansibledocker:$DOCKER_TAG"
            }
        }
        stage('Docker Deploy'){
            steps{
              ansiblePlaybook credentialsId: 'docker-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'Ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}