pipeline {
    agent any
    
    tools {
        maven '3.9.11'
    }

    environment {
        NEXUS_URL = '192.168.1.100:8081' // Use your VM IP
        CREDENTIALS_ID = 'nexus-credentials'
    }

    stages {
        // NO CHECKOUT STAGE NEEDED - Jenkins does this automatically
        
        stage('Build and Package') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Push to Nexus') {
            steps {
                script {
                    def pom = readMavenPom file: 'pom.xml'
                    def artifactId = pom.artifactId
                    def version = pom.version
                    def groupId = pom.groupId
                    def jarPath = "target/${artifactId}-${version}.jar"
                    def targetRepo = version.endsWith("-SNAPSHOT") ? "maven-snapshots" : "maven-releases"
                    
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
        failure { echo "Build or Upload failed." }
    }
}
