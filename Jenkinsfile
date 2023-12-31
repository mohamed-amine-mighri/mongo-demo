pipeline {
    agent any // Add this line to specify the agent

    tools {
        maven 'Maven'
    }

    stages {

        stage('Build') {
            steps {
                script {
                    // sh 'mvn test'
                    sh 'mvn clean package -DskipTests'
                    sh 'docker-compose -f docker-compose.yml build -t aminemighri/mongo-demo .'
                }
            }
        }
        stage('SonarQube Analysis') {
            steps{
                script{
                    def mvn = tool 'Maven';
                    withSonarQubeEnv() {
                        sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=amine-app-scan -Dsonar.projectName='amine-app-scan'"
                    }
                }
            }
        }
        stage("Build Image") {
            steps {
                script {
                    echo 'Building Docker image...'

                    // Use the credentials plugin to safely handle username and password
                    withCredentials([usernamePassword(credentialsId: 'amineDockerHub', passwordVariable: 'PASS', usernameVariable: 'USER')]) {

                        // Ensure the Docker daemon is running
                        sh 'docker info'

                        // Build the Docker image using the specified docker-compose file
                        sh 'docker-compose -f docker-compose.yml build -t aminemighri/mongo-demo .'

                        // Tag the built image
                        sh 'docker tag aminemighri/mongo-demo aminemighri/mongo-demo:latest'

                        // Log in to Docker Hub
                        sh "echo $PASS | docker login -u $USER --password-stdin"

                        // Push the image to Docker Hub
                        sh 'docker push aminemighri/mongo-demo:latest'
                    }
                }
            }
        }
        stage('Pull image for deployment') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'amineDockerHub', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker pull aminemighri/mongo-demo:latest'
                    sh 'docker-compose -f docker-compose.yml build -t aminemighri/mongo-demo .'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'KuberConf']) {
                    sh """
                          minikube cache add aminemighri/mongo-demo:latest
                          minikube cache reload
                          minikube kubectl -- apply -f ./k8s/mongo-demo-deployment.yaml
                          minikube kubectl -- apply -f ./k8s/mongodb-deployment.yaml
                    """
                    }
                }
            }
        }
    }
    post {
        always {
             emailext attachLog: true,
             subject: "'${currentBuild.result}'",
             body: "Project: ${env.JOB_NAME}<br/>" +
             "Build Number: ${env.BUILD_NUMBER}<br/>" +
             "URL: ${env.BUILD_URL}<br/>",
              to: 'mighrimedamine330@gmail.com'
        }
    }
}
