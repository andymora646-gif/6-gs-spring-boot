pipeline {
    agent any
    
    environment {
        // REPLACE with your actual Ubuntu VM IP
        NEXUS_URL = '192.168.1.100:8081' 
        CREDENTIALS_ID = 'nexus-credentials'
    }

    stages {
        stage('Push Existing Jar to Nexus') {
            steps {
                script {
                    // Since pom.xml is missing, we hardcode the metadata for the lab
                    def artifactId = "spring-boot-complete"
                    def version = "0.0.1-SNAPSHOT"
                    def groupId = "com.example"
                    def jarPath = "target/spring-boot-complete-0.0.1-SNAPSHOT.jar"
                    
                    echo "Uploading existing artifact to Nexus..."

                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${NEXUS_URL}",
                        groupId: "${groupId}",
                        version: "${version}",
                        repository: "maven-snapshots",
                        credentialsId: "${CREDENTIALS_ID}",
                        artifacts: [
                            [artifactId: "${artifactId}", classifier: '', file: "${jarPath}", type: 'jar']
                        ]
                    )
                }
            }
        }
    }
}
