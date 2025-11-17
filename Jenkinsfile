pipeline {
    agent any

    environment {
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'adam'   // <- fix this
        NEXUS_REPO = 'springboot-hw6'
        NEXUS_URL  = 'http://host.docker.internal:8081'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'chmod +x gradlew'
                sh './gradlew clean build -x test'
            }
        }

        stage('Upload to Nexus') {
            steps {
                sh '''
                  echo "Finding JAR file..."
                  JAR_FILE=$(ls build/libs/*.jar | head -n 1)
                  echo "Using JAR: $JAR_FILE"

                  BASENAME=$(basename "$JAR_FILE")
                  echo "Uploading $JAR_FILE to Nexus raw repo ${NEXUS_REPO} as ${BASENAME}..."

                  curl --fail -v -u ${NEXUS_USER}:${NEXUS_PASS} \
                       --upload-file "$JAR_FILE" \
                       ${NEXUS_URL}/repository/${NEXUS_REPO}/${BASENAME}

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
