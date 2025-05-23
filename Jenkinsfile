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
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                  sh 'sonar-scanner'
                script { // Added a script block for better control and clarity
                    // --- DEBUGGING STEP: List files in the workspace ---
                    echo "Listing files in current directory before SonarScan:"
                    sh 'ls -la'
                    echo "--- End of file listing ---"
                    // --- END DEBUGGING STEP ---

                    withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                        // Explicitly tell sonar-scanner the project base directory
                        sh 'sonar-scanner -Dsonar.projectBaseDir=$WORKSPACE -Dsonar.token=$SONAR_TOKEN -Dsonar.projectKey=akshitpatel1732_SIT753-Task8.1C-Part-2-Task-1 -Dsonar.organization=akshitpatel1732 -Dproject.settings=sonar-project.properties'
                    }
                }
            }
        }
    }
}
}