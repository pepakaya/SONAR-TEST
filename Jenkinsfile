pipeline {
    agent {
        docker {
            image 'python:3.9'  // Official Python 3.9 Docker image
            args '-u root'  // Run as root user to install additional tools
        }
    }

    environment {
        SONAR_HOST_URL = 'http://desktop-sonarqube-1:9000'
        SONAR_PROJECT_KEY = 'My-sonar-test'
        COVERAGE_REPORT_PATH = 'coverage.xml'
    }

    stages {
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
                    pip install coverage  # Install coverage.py
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
                withSonarQubeEnv('SonarQubeServer') {
                    script {
                        def scannerHome = tool 'SonarQube Scanner'

                        withCredentials([string(credentialsId: 'sonar-auth-token', variable: 'SONARQUBE_AUTH_TOKEN')]) {
                            sh """
                                ${scannerHome}/bin/sonar-scanner \
                                -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                                -Dsonar.sources=. \
                                -Dsonar.host.url=${SONAR_HOST_URL} \
                                -Dsonar.token=$SONARQUBE_AUTH_TOKEN \
                                -Dsonar.python.coverage.reportPaths=${COVERAGE_REPORT_PATH} \
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
    }
}
