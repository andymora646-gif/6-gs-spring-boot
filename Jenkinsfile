pipeline {
    agent any
    
environment {
    // Change this from 192.168.1.100 to localhost
    NEXUS_URL = 'localhost:8081' 
    NEXUS_CREDS = credentials('nexus-credentials')
}

    stages {
        stage('Push to Nexus (Manual Upload)') {
            steps {
                script {
                    def jarFile = "target/spring-boot-complete-0.0.1-SNAPSHOT.jar"
                    def repoUrl = "http://${NEXUS_URL}/repository/maven-snapshots/com/example/spring-boot-complete/0.0.1-SNAPSHOT/spring-boot-complete-0.0.1-SNAPSHOT.jar"
                    
                    echo "Uploading to: ${repoUrl}"
                    
                    // Using CURL to bypass plugin metadata errors
                    sh """
                    curl -v -u ${NEXUS_CREDS_USR}:${NEXUS_CREDS_PSW} \
                    --upload-file ${jarFile} \
                    ${repoUrl}
                    """
                }
            }
        }
    }
    
    post {
        success { echo "Check Nexus now! The file should be there." }
        failure { echo "Still failing. Check the 'curl' output above for a 401 (Auth) or 404 (URL) error." }
    }
}
