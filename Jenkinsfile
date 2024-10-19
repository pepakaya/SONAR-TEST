pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'http://desktop-sonarqube-1:9000'  // SonarQube container name or use IP if necessary
        SONAR_PROJECT_KEY = 'My-sonar-test'  // Your SonarQube project key
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm  // Checkout your source code from the version control system
            }
        }

        stage('Build') {
            steps {
                echo "No build step for Python analysis"
                // If needed, you can run build or test steps for Python projects here
                // Example: sh 'python3 -m unittest discover tests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {  // Ensure 'SonarQubeServer' is configured in Jenkins
                    script {
                        // Adjust the tool name if necessary based on your Jenkins configuration
                        def scannerHome = tool 'SonarQube Scanner';  // Use the installed SonarQube Scanner tool

                        withCredentials([string(credentialsId: 'sonar-auth-token', variable: 'SONARQUBE_AUTH_TOKEN')]) {
                            // Execute SonarQube scanner
                            sh "${scannerHome}/bin/sonar-scanner " +
                               "-Dsonar.projectKey=${SONAR_PROJECT_KEY} " +
                               "-Dsonar.sources=. " +  // Analyze all files in the current workspace
                               "-Dsonar.host.url=${SONAR_HOST_URL} " +  // Use the SonarQube host URL
                               "-Dsonar.login=${SONARQUBE_AUTH_TOKEN} " +  // Use the stored authentication token
                               "-Dsonar.python.version=3.9"  // Specify your exact Python version
                        }
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {  // Increased timeout for Quality Gate
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
