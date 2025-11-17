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
                // make the wrapper executable, THEN run it
                sh 'chmod +x gradlew'
                sh './gradlew clean build -x test'
            }
        }

        stage('Upload to Nexus') {
            steps {
                sh '''
                    # find the built jar
                    JAR_FILE=$(ls build/libs/*.jar | head -n 1)

                    echo "Uploading $JAR_FILE to Nexus..."

                    curl -v -u admin:admin123 \
                      --upload-file "$JAR_FILE" \
                      "http://localhost:8081/repository/maven-releases/com/example/springboot-hw6/1.0.0/springboot-hw6-1.0.0.jar"
                '''
            }
        }
    }
}
