pipeline {
    agent any

    tools {
        jdk 'JDK_21'
        maven 'Maven_3.9'
    }

    stages {

        /**********************
         * CHECKOUT CODE
         **********************/
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        /**********************
         * BUILD + TEST + COVERAGE
         * (Single Maven run for efficiency)
         **********************/
        stage('Build & Test') {
            steps {
                bat "mvn clean verify"
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        /**********************
         * SONARQUBE ANALYSIS
         **********************/
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube_BloodBank_Server') {
                    bat """
                    mvn sonar:sonar ^
                        -Dsonar.projectKey=BloodBank ^
                        -Dsonar.host.url=http://localhost:9002
                    """
                }
            }
        }

        /**********************
         * QUALITY GATE CHECK
         **********************/
        stage("Quality Gate") {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        /**********************
         * PACKAGE ARTIFACT
         **********************/
        stage('Package') {
            steps {
                bat "mvn -DskipTests=true package"
                archiveArtifacts artifacts: 'target/*.war', fingerprint: true
            }
        }

        /**********************
         * DEPLOY TO TOMCAT (optional)
         * Enable when ready
         **********************/
        stage('Deploy') {
            steps {
                echo "Deployment stage placeholder..."
                // Uncomment when ready:
                // bat "curl --upload-file target/BloodBank.war http://tomcat_user:tomcat_pass@localhost:8087/manager/text/deploy?path=/BloodBank&update=true"
            }
        }
    }

    /**********************
     * NOTIFICATIONS (optional)
     **********************/
    post {
        success {
            echo "Build finished successfully!"
        }
        failure {
            echo "Build failed!"
        }
    }
}
