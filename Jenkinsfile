pipeline {
    agent any

    environment {
        // Nexus repo URL as seen from INSIDE the Jenkins container
        NEXUS_REPO_URL = 'http://host.docker.internal:8081/repository/springboot-hw6/'
        NEXUS_CRED_ID = 'nexus-admin'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh './gradlew clean build -x test'
            }
        }

        stage('Upload to Nexus') {
            steps {
                script {
                    // find the jar Gradle built
                    def jarFile = sh(
                        script: "ls build/libs/*.jar",
                        returnStdout: true
                    ).trim()

                    echo "Uploading ${jarFile} to Nexus..."

                    withCredentials([usernamePassword(
                        credentialsId: NEXUS_CRED_ID,
                        usernameVariable: 'NEXUS_USER',
                        passwordVariable: 'NEXUS_PASS'
                    )]) {
                        sh """
                            curl -v -u $NEXUS_USER:$NEXUS_PASS \
                              --upload-file "${jarFile}" \
                              "${NEXUS_REPO_URL}\$(basename ${jarFile})"
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
        }
    }
}
