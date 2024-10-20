pipeline {
    agent any

    environment {
        SONAR_PROJECT_KEY = 'My-sonar-test'  // Your SonarQube project key
        QUALITY_PROFILE = 'sonar-python-test'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm  // Checkout your source code from the version control system
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {  // Ensure 'SonarQubeServer' is configured in Jenkins
                    script {
                        // Use the installed SonarQube Scanner tool
                        def scannerHome = tool 'SonarQube Scanner'

                        // Execute SonarQube scanner securely
                        sh """${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                            -Dsonar.qualityprofile=${QUALITY_PROFILE} \
                            -X"""  // Added -X for full debug logging
                    }
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
