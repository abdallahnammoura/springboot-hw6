pipeline {
    agent any

    environment {
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
                    JAR_FILE=$(ls build/libs/*.jar | head -n 1)

                    echo "Uploading $JAR_FILE to Nexus..."

                    curl -v -u admin:admin123 \
                        --upload-file "$JAR_FILE" \
                        "http://nexus:8081/repository/maven-releases/com/example/springboot-hw6/1.0.0/springboot-hw6-1.0.0.jar"
                '''
            }
        }
    }
}
