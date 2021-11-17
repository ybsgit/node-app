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
            steps{
            withCredentials([string(credentialsId: 'docker pass', variable: 'dockerHubPwd')]) {
         sh "docker login -u web927 -p ${dockerHubPwd}"
         sh "docker push web927/nodeapp:${DOCKER_TAG}"
           }
            } 
        }
        stage('Deploy to k8s'){
            steps{
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
            sshagent(['sshk8s']) {
               sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml root@10.182.0.33:/root/cicd"
               sh "ssh -o StrictHostKeyChecking=no root@10.182.0.33 /usr/bin/kubectl apply -f services.yml &&  /usr/bin/kubectl apply -f  node-app-pod.yml"
                }
            }
        }

    }
}

def getDockerTag(){
    def tag = sh script: 'git rev-parse HEAD',returnStdout: true
    return tag 
}