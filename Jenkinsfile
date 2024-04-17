pipeline {
    agent any

    stages {
        stage('Build Artifact') {
            steps {
                script {
                    sh 'mvn clean package -DskipTests=true'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }

        stage('unit test - JUnit and Jacoco') {
            steps {
                script {
                    sh "mvn test"
                }
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                    jacoco(execPattern: 'target/jacoco.exec')
                }
            }
        }

        stage('Docker build and Push') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "printenv"
                        sh "docker build -t keasar/numeric-app:${GIT_COMMIT} ."
                        sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                        sh "docker push keasar/numeric-app:${GIT_COMMIT}"
                    }
                }
            }
        }
        stage('Kubernetes Deployment - DEV') {
            steps {
                
                    withKubeConfig([usernamePassword(credentialsId: 'kubeconfig')]) {
                        sh "sed -i 's#replace#keasar/numeric-app:${GIT_COMMIT} #g' k8s_deployment_service.yaml"
                        sh "kubectl apply -f k8s_deployment_service.yaml "
                        
                    }
                
            }
        }
    }
}
