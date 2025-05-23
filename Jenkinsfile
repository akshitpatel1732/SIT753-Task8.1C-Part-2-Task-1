pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/akshitpatel1732/8.2CDevSecOps.git'
                // --- NEW DEBUGGING STEP: List files AFTER Git checkout ---
                echo "Listing files in workspace AFTER Git checkout:"
                sh 'ls -la'
                echo "--- End of checkout file listing ---"
                // --- END DEBUGGING STEP ---
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
        // --- SONARCLOUD ANALYSIS STAGE ---
        stage('SonarCloud Analysis') {
            steps {
                script {
                    // This 'ls -la' is useful as a secondary check, but the one in 'Checkout' is more critical
                    echo "Listing files in current directory before SonarScan (redundant if Checkout shows all files):"
                    sh 'ls -la'
                    echo "--- End of file listing ---"

                    withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                        // sonar-scanner will pick up sonar.login from the SONAR_TOKEN env var
                        // and project base dir is often '.' by default, but $WORKSPACE is explicit.
                        sh 'sonar-scanner -Dsonar.projectBaseDir=$WORKSPACE'
                    }
                }
            }
        }
    }
}
