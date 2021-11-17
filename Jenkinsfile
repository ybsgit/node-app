pipeline{
    agent any
    environment{
        DOCKER_TAG= getDockerTag()
    }
    stages{
        stage('Build docker image'){
            steps{
                sh 'docker build . -t web927/nodeapp:${DOCKER_TAG}'

            }
        }
        stage('dockerhub push'){
            withCredentials([string(credentialsId: 'docker pass', variable: 'dockerHubPwd')]) {
         sh "docker login -u web927 -p ${dockerHubPwd}"
         sh "docker push web927/nodeapp:${DOCKER_TAG}"
           }
            
        }
    }
}

def getDockerTag(){
    def tag = sh script: 'git rev-parse HEAD',returnStdout: true
    return tag 
}