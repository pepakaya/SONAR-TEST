pipeline {
    agent any

    tools {
        sonarScanner 'SonarQube Scanner'  // Reference to SonarQube Scanner configured in Jenkins
    }

    environment {
        SONAR_HOST_URL = 'http://desktop-sonarqube-1:9000'  // SonarQube container name or IP
        SONAR_PROJECT_KEY = 'My-sonar-test'  // Your SonarQube project key
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Checkout your source code from your version control system (e.g., Git)
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "No build step for Python analysis"
                // You can run build or test steps for Python projects here if needed
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Perform SonarQube analysis
                withSonarQubeEnv('My SonarQube') {  // 'My SonarQube' is the name configured in Jenkins
                    script {
                        def scannerHome = tool 'SonarQube Scanner'  // Use the SonarQube Scanner tool

                        withCredentials([string(credentialsId: 'sonar-auth-token', variable: 'SONARQUBE_AUTH_TOKEN')]) {
                            // Execute SonarQube scanner without backslashes
                            sh "${scannerHome}/bin/sonar-scanner " +
                                "-Dsonar.projectKey=${SONAR_PROJECT_KEY} " +
                                "-Dsonar.sources=. " +  // Analyze all files in the current workspace
                                "-Dsonar.host.url=${SONAR_HOST_URL} " +  // Use the SonarQube host URL
                                "-Dsonar.login=${SONARQUBE_AUTH_TOKEN} " +  // Use the stored authentication token
                                "-Dsonar.language=py " +  // Specify Python as the language
                                "-Dsonar.python.version=3.x"  // Specify your Python version (e.g., 3.9)
                        }
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                // Wait for SonarQube Quality Gate to complete
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true  // Fail the pipeline if the quality gate fails
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        success {
            echo 'SonarQube analysis completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
