pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/akshitpatel1732/8.2CDevSecOps.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test || true' // Allows pipeline to continue despite test failures
            }
        }
        stage('Generate Coverage Report') {
            steps {
                // Ensure coverage report exists
                sh 'npm run coverage || true'
            }
        }
        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true' // This will show known CVEs in the output
            }
        }
        // --- NEW SONARCLOUD ANALYSIS STAGE ---
        stage('SonarCloud Analysis') {
            steps {
                script {
                    // Use withCredentials to securely inject SONAR_TOKEN as an environment variable
                    withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                        // Define SonarScanner CLI version and download URL
                        // ALWAYS check the official SonarSource website for the latest stable version!
                        def SONAR_SCANNER_VERSION = '7.1.0.4889' 
                        def SONAR_SCANNER_ZIP = "sonar-scanner-cli-${SONAR_SCANNER_VERSION}-linux.zip"
                        def SONAR_SCANNER_URL = "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/${SONAR_SCANNER_ZIP}"
                        def SONAR_SCANNER_DIR = "sonar-scanner-${SONAR_SCANNER_VERSION}-linux" // Extracted directory name

                        sh '''
                            echo "Downloading SonarScanner CLI..."
                            # Download the SonarScanner CLI .zip file
                            wget -q ''' + SONAR_SCANNER_URL + ''' -O ''' + SONAR_SCANNER_ZIP + '''

                            echo "Extracting SonarScanner CLI..."
                            # Extract the archive
                            unzip -q ''' + SONAR_SCANNER_ZIP + '''

                            echo "Running SonarCloud analysis..."
                            # Execute the SonarScanner.
                            # It will automatically pick up sonar-project.properties and the SONAR_TOKEN environment variable.
                            ./''' + SONAR_SCANNER_DIR + '''/bin/sonar-scanner
                        '''
                    }
                }
            }
    }
}
