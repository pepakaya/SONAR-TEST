pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'http://desktop-sonarqube-1:9000'  // SonarQube server URL
        SONAR_PROJECT_KEY = 'My-sonar-test'  // Your SonarQube project key
        COVERAGE_REPORT_PATH = 'coverage.xml'  // Location of the coverage report
    }

    stages {
        stage('Install Python') {
            steps {
                sh '''
                # Install Python 3.9 (or any version you need)
                apt-get update
                apt-get install -y python3 python3-pip

                # Verify Python installation
                python3 --version
                '''
            }
        }

        stage('Checkout Code') {
            steps {
                checkout scm  // Checkout your source code
            }
        }

        stage('Install Coverage.py') {
            steps {
                script {
                    // Install coverage.py for code coverage
                    sh '''
                    python3 -m pip install --upgrade pip
                    pip install coverage  # Install only coverage.py
                    '''
                }
            }
        }

        stage('Run Tests and Generate Coverage Report') {
            steps {
                script {
                    // Run tests with coverage and generate the coverage.xml file
                    sh '''
                    coverage run -m unittest discover  # Adjust for your test framework
                    coverage xml -o ${COVERAGE_REPORT_PATH}  # Generate coverage.xml
                    '''
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {  // Ensure 'SonarQubeServer' is configured in Jenkins
                    script {
                        def scannerHome = tool 'SonarQube Scanner'

                        withCredentials([string(credentialsId: 'sonar-auth-token', variable: 'SONARQUBE_AUTH_TOKEN')]) {
                            // Execute SonarQube scanner with additional parameters for coverage
                            sh """
                                ${scannerHome}/bin/sonar-scanner \
                                -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                                -Dsonar.sources=. \
                                -Dsonar.host.url=${SONAR_HOST_URL} \
                                -Dsonar.token=$SONARQUBE_AUTH_TOKEN \
                                -Dsonar.python.version=3.9 \
                                -Dsonar.python.coverage.reportPaths=${COVERAGE_REPORT_PATH} \
                                -Dsonar.coverage.exclusions=tests/**/*  # Exclude test files from coverage \
                                -X
                            """
                        }
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
            echo 'SonarQube analysis and coverage check completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
