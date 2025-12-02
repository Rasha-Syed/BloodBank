pipeline {
    agent any

    tools {
        jdk 'JDK_21'
        maven 'Maven_3.9'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Modified: Removed 'verify' because it triggers test phases you don't need.
                // 'clean package' is sufficient to create the WAR file.
                bat "mvn clean package"
            }
            // Removed the 'post' block with 'junit' entirely. 
            // You have no tests, so you do not need to publish test results.
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube_BloodBank_Server') {
                    // Added -Dsonar.sources to force it to look at webapp
                    bat "mvn sonar:sonar -Dsonar.projectKey=BloodBank -Dsonar.host.url=http://localhost:9002 -Dsonar.sources=src/main/webapp"
                }
            }
        }

        /*stage("Quality Gate") {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }*/

        stage('Package') {
            steps {
                // Since we ran 'package' in the Build stage, the WAR is already there.
                // We just archive it here.
                archiveArtifacts artifacts: 'target/*.war', fingerprint: true
            }
        }

        stage('Deploy') {
            steps {
                echo "Deployment stage placeholder..."
                // bat "curl --upload-file target/BloodBank.war http://tomcat_user:tomcat_pass@localhost:8087/manager/text/deploy?path=/BloodBank&update=true"
            }
        }
    }

    post {
        success {
            echo "Build finished successfully!"
        }
        failure {
            echo "Build failed!"
        }
    }
}
