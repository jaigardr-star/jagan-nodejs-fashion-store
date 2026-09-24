pipeline {
    agent any

    stages {

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    script {
                        def scannerHome = tool 'SonarScanner'
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t jagan-nodejs-fashion-store:latest .'
            }
        }

        stage('Remove Old Container') {
            steps {
                sh 'docker rm -f jagan-fashion-app || true'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker run -d \
                    --name jagan-fashion-app \
                    -p 3001:3000 \
                    jagan-nodejs-fashion-store:latest
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    sleep 5
                    docker ps --filter name=jagan-fashion-app
                    curl -f http://localhost:3001/
                '''
            }
        }
    }
}
