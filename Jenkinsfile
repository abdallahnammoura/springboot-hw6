pipeline {
    agent any

    environment {
        // Optional, but good hygiene if you ever need it
        MAVEN_OPTS = "-Dmaven.test.skip=true"
    }

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
                    echo "Finding built JAR..."
                    # Prefer the plain jar if it exists, otherwise any jar in build/libs
                    JAR_FILE=$(ls build/libs/*-plain.jar build/libs/*.jar 2>/dev/null | head -n 1)

                    if [ -z "$JAR_FILE" ]; then
                      echo "ERROR: No JAR found in build/libs"
                      exit 1
                    fi

                    echo "Uploading $JAR_FILE to Nexus..."

                    curl -v -u admin:admin123 \
                      --upload-file "$JAR_FILE" \
                      "http://host.docker.internal:8081/repository/maven-releases/com/example/springboot-hw6/1.0.0/springboot-hw6-1.0.0.jar"
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
