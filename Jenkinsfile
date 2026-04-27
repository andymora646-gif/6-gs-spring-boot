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
                    // This line fixes the 'No such property: POM' error
                    def pom = readMavenPom file: 'pom.xml'
                    
                    // Now you can use 'pom' variable to get version and artifactId
                    def artifactId = "${pom.artifactId}"
                    def version = "${pom.version}"
                    def groupId = "${pom.groupId}"
                    def jarPath = "target/${artifactId}-${version}.jar"
        
                    echo "Uploading ${jarPath} to Nexus..."
                    
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3', // or 'nexus2' depending on your setup
                        protocol: 'http',
                        nexusUrl: 'your-nexus-ip:8081',
                        groupId: groupId,
                        version: version,
                        repository: 'maven-releases', // Use 'maven-snapshots' if version ends in -SNAPSHOT
                        credentialsId: 'nexus-credentials', // The ID you created in Jenkins
                        artifacts: [
                            [artifactId: artifactId, classifier: '', file: jarPath, type: 'jar']
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
