pipeline
{
    agent any
    stages{
        stage ('Create Image'){
            steps{
                script{
                   dockerapp = docker.build("deivisson/kube-news:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')     
                }
            }
        }

        stage ('Push Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com/', 'dockerhub'){
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }   
                }
            }
        }

        stage ('Deploy Kubernetes') {
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'sed -i "s/{{TAG}}/$tag_version/g" ./src/deployment.yaml'
                    sh 'kubectl apply -f ./src/deployment.yaml'
                }
            }
        }
    }
}