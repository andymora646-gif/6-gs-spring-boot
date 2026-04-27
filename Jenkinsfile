pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
    }

    tools {
        maven '3.9.11'
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git branch: 'main', url: 'https://github.com/kevinli-webbertech/gs-spring-boot.git'
            }
        }

        stage('Test') {
            steps {
                sh 'git --version'
                sh 'mvn --version'
                sh 'mvn clean test' // Example for a Maven project
            }
        }

        stage('Build and Package') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
         stage('Push to Nexus') {
            steps {
                script {
                    // Extract version/artifact ID from POM
                    def artifactId = "${POM.artifactId}"
                    def version = "${POM.version}"
                    def jarPath = "target/${artifactId}-${version}.jar"
                    
                    echo "Uploading ${jarPath} to ${NEXUS_REPO}..."

                    nexusArtifactUploader(
                        nexusVersion: "${NEXUS_VERSION}",
                        protocol: "${PROTOCOL}",
                        nexusUrl: "${NEXUS_URL}",
                        groupId: "${POM.groupId}",
                        version: "${version}",
                        repository: "${NEXUS_REPO}",
                        credentialsId: "${CREDENTIALS_ID}",
                        artifacts: [
                            [artifactId: "${artifactId}",
                             classifier: '',
                             file: "${jarPath}",
                             type: 'jar']
                        ]
                    )
                }
            }
         }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build successful!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
