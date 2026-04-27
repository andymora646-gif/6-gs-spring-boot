pipeline {
    agent any
    
    tools {
        maven '3.9.11'
    }

    environment {
        // REPLACE with your actual Ubuntu VM IP
        NEXUS_URL = '192.168.1.100:8081' 
        CREDENTIALS_ID = 'nexus-credentials'
    }

    stages {
        stage('Check Workspace') {
            steps {
                // This will list files so you can see where pom.xml is in the logs
                sh 'ls -R' 
            }
        }

        stage('Build and Package') {
            steps {
                // Running directly in the root
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Push to Nexus') {
            steps {
                script {
                    // Fixes "No such property: POM" error
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
        failure {
            echo 'Build failed. Check the "Check Workspace" stage log to see where pom.xml is.'
        }
    }
}
