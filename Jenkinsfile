pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                    chmod +x gradlew
                    ./gradlew clean build -x test
                '''
            }
        }

        stage('Upload to Nexus') {
            steps {
                sh '''
                    echo "Finding JAR file..."
                    JAR_FILE=$(ls build/libs/*.jar | head -n 1)

                    echo "Uploading $JAR_FILE to Nexus raw repo springboot-hw6..."
                    curl --fail -v -u admin:admin123 \
                        --upload-file "$JAR_FILE" \
                        http://nexus:8081/repository/springboot-hw6/$(basename "$JAR_FILE")

                    echo "Upload complete."
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
        }
    }
}

