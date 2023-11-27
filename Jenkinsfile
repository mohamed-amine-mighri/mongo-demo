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

        stage("build image") {
            steps {
                script {
                    echo 'building docker image...'
                    withCredentials([usernamePassword(credentialsId: 'amine-docker', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh 'docker-compose up -t aminemighri/mongo-demo .'
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh 'docker push aminemighri/mongo-demo'
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