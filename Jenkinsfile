pipeline {
    agent any
    
    tools {
        maven '3.9.11'
    }

    environment {
        // CHANGE THIS to your Ubuntu VM IP
        NEXUS_URL = '192.168.1.100:8081' 
        CREDENTIALS_ID = 'nexus-credentials'
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git branch: 'main', url: 'https://github.com'
            }
        }

        stage('Build and Package') {
            steps {
                // Skips tests to speed up the lab as per your logs
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Push to Nexus') {
            steps {
                script {
                    // 1. Fix the error by reading the POM file into a variable
                    def pom = readMavenPom file: 'pom.xml'
                    
                    // 2. Extract metadata
                    def artifactId = pom.artifactId
                    def version = pom.version
                    def groupId = pom.groupId
                    def jarPath = "target/${artifactId}-${version}.jar"
                    
                    // 3. Logic to determine destination repository
                    def targetRepo = version.endsWith("-SNAPSHOT") ? "maven-snapshots" : "maven-releases"
                    
                    echo "Uploading ${artifactId} version ${version} to ${targetRepo}"

                    // 4. Perform the upload
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${NEXUS_URL}",
                        groupId: "${groupId}",
                        version: "${version}",
                        repository: "${targetRepo}",
                        credentialsId: "${CREDENTIALS_ID}",
                        artifacts: [
                            [artifactId: "${artifactId}", classifier: '', file: "${jarPath}", type: 'jar']
                        ]
                    )
                }
            }
        }
    }

    post {
        success {
            echo "Successfully pushed ${env.JOB_NAME} to Nexus!"
        }
        failure {
            echo "Build or Upload failed. Check the logs above."
        }
    }
}
