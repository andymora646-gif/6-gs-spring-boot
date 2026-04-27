pipeline {
    agent any
    
    tools {
        // Ensure this matches the name you gave Maven in 'Global Tool Configuration'
        maven '3.9.11'
    }

    environment {
        // REPLACE with your Ubuntu VM IP
        NEXUS_URL = '192.168.1.100:8081' 
        // Ensure this matches the ID in Manage Jenkins > Credentials
        CREDENTIALS_ID = 'nexus-credentials'
    }

    stages {
        stage('Build and Package') {
            steps {
                // Navigate into the folder containing pom.xml
                dir('complete') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Push to Nexus') {
            steps {
                script {
                    // Navigate into the folder so readMavenPom can find the file
                    dir('complete') {
                        // Fixes "No such property: POM" error
                        def pom = readMavenPom file: 'pom.xml'
                        
                        def artifactId = pom.artifactId
                        def version = pom.version
                        def groupId = pom.groupId
                        def jarPath = "target/${artifactId}-${version}.jar"
                        
                        // Automatically chooses Snapshot or Release repo
                        def targetRepo = version.endsWith("-SNAPSHOT") ? "maven-snapshots" : "maven-releases"
                        
                        echo "Uploading ${artifactId} to ${targetRepo}..."

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
    }

    post {
        success {
            echo 'Build and Nexus Upload Successful!'
        }
        failure {
            echo 'Build failed. Check if the folder name in dir() is correct and Nexus is running.'
        }
    }
}
