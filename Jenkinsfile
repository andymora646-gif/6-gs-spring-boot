pipeline {
    agent any
    
    environment {
        // REPLACE with your Ubuntu VM IP (where Nexus is running)
        NEXUS_URL = '192.168.1.100:8081' 
        // Ensure this ID matches what you created in Jenkins Credentials
        CREDENTIALS_ID = 'nexus-credentials'
    }

    stages {
        stage('Push to Nexus') {
            steps {
                script {
                    // Hardcoding metadata since your GitHub repo is missing the pom.xml file
                    def artifactId = "spring-boot-complete"
                    def version = "0.0.1-SNAPSHOT"
                    def groupId = "com.example"
                    
                    // We point to the target/ folder where we saw the JAR in your previous logs
                    def jarPath = "target/spring-boot-complete-0.0.1-SNAPSHOT.jar"
                    
                    echo "Uploading ${jarPath} to Nexus at ${NEXUS_URL}..."

                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${NEXUS_URL}",
                        groupId: "${groupId}",
                        version: "${version}",
                        repository: "maven-snapshots",
                        credentialsId: "${CREDENTIALS_ID}",
                        artifacts: [
                            [
                                artifactId: "${artifactId}", 
                                classifier: '', 
                                file: "${jarPath}", 
                                type: 'jar'
                            ]
                        ]
                    )
                }
            }
        }
    }

    post {
        success {
            echo "Successfully pushed to Nexus! Check http://${NEXUS_URL}/#browse/browse:maven-snapshots"
        }
        failure {
            echo "Upload failed. 1. Check if Nexus is up. 2. Verify 'Strict Content Type Validation' is OFF in Nexus."
        }
    }
}
