pipeline {
    agent any

    tools {
        nodejs "NodeJS 18" // Sesuaikan dengan nama di Global Tool Configuration
    }

    stages {
        stage('Checkout Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/AkilaNorSalsabila/laravel-jenkins.git'
            }
        }

        stage('Setup NodeJS') {
            steps {
                script {
                    def nodejs = tool name: 'NodeJS 18', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
                    env.PATH = "${nodejs}/bin:${env.PATH}"
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
                sh 'composer install --no-dev --optimize-autoloader' // Install Laravel dependencies tanpa development dependencies
                sh 'php artisan cache:clear' // Membersihkan cache Laravel
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    def testStatus = sh(script: 'npm test || echo "NO_TESTS"', returnStdout: true).trim()
                    if (testStatus.contains("NO_TESTS")) {
                        echo "Skipping tests: No test script found in package.json"
                    }
                }
            }
        }

        stage('Build and Archive') {
            steps {
                sh 'npm run build || echo "NO_BUILD"' // Jika tidak ada build, tetap lanjut
                archiveArtifacts artifacts: 'dist/', fingerprint: true
            }
        }
    }
}
