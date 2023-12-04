pipeline {
    agent any // Add this line to specify the agent

    tools {
        maven 'Maven'
    }

    stages {
        stage("build jar") {
            steps {
                script {
                    echo 'building the app...'
                    sh 'mvn clean package'
                }
            }
        }

    stage("Build Image") {
        steps {
            script {
                echo 'Building Docker image...'

                // Use the credentials plugin to safely handle username and password
                withCredentials([usernamePassword(credentialsId: 'dockerHubCred', passwordVariable: 'PASS', usernameVariable: 'USER')]) {

                    // Ensure the Docker daemon is running
                    sh 'docker info'

                    // Build the Docker image using the specified docker-compose file
                    sh 'docker-compose -f docker-compose.yml build'

                    // Verify that the image is built successfully before tagging
                    sh 'docker images'

                    // Tag the built image
                    sh 'docker tag mongo-demo aminemighri/mongo-demo'

                    // Log in to Docker Hub
                    sh "echo $PASS | docker login -u $USER --password-stdin"

                    // Push the image to Docker Hub
                    sh 'docker push aminemighri/mongo-demo:latest'
                }
            }
        }
    }



        stage("deploy") {
            steps {
                script {
                    echo "deploying the application..."
                    // Add your deployment steps here
                }
            }
        }

//         stage('SCM') {
//             steps {
//                 checkout scm
//             }
//         }

//         stage('SonarQube Analysis') {
//             steps {
//                 script {
//                     def mvn = tool 'Maven';
//                     withSonarQubeEnv() {
//                         sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=amine-app-scan -Dsonar.projectName='amine-app-scan'"
//                     }
//                 }
//             }
//         }
    }
}