pipeline {
    agent any

    stages {
        stage('Build Artifact') {
            steps {
                script {
                    sh 'mvn clean package -DskipTests=true'
                    archiveArtifacts 'target/*.jar' // Use archiveArtifacts instead of archive
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
                 sh 'printenv'
                  sh 'docker build -t keasar/numeric-app:""$GIT_COMMIT""'
                   sh 'docker push keasar/numeric-app:""$GIT_COMMIT""'
            }
        }
    }
}
