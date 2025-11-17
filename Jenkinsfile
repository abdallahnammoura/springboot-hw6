pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                // make mvnw executable inside the Linux Jenkins container
                sh 'chmod +x mvnw'
                // build the Spring Boot jar, skip tests to keep it fast
                sh './mvnw -B clean package -DskipTests'
            }
        }
    }

    post {
        success {
            // archive the jar so you can see it in Jenkins UI
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
    }
}
