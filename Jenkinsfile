pipeline {
    agent any

    tools {
        maven 'Maven-3.8.1'   // Matches the name in Jenkins
        jdk 'JDK-17'          // Matches the name in Jenkins
    }

    environment {
        MAVEN_OPTS = '-Dmaven.repo.local=.m2/repository'
    }

    stages {
        stage('Checkout') {
            steps {
                echo '📥 Checking out code from GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo '🔨 Building the application...'
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Running tests...'
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                echo '📦 Packaging the application...'
                sh 'mvn package -DskipTests'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo '🚀 Deploying to staging...'
                sh '''
                    echo "Stopping existing application..."
                    pkill -f "demo-1.0.0.jar" || true

                    echo "Starting new application..."
                    nohup java -jar target/demo-1.0.0.jar --server.port=8080 > app.log 2>&1 &

                    echo "Waiting for app startup..."
                    sleep 15

                    echo "Checking health endpoint..."
                    curl -f http://localhost:8080/health || exit 1
                '''
            }
        }

        stage('Integration Tests') {
            steps {
                echo '🔍 Running integration tests...'
                sh '''
                    curl -f http://localhost:8080/ || exit 1
                    curl -f http://localhost:8080/hello || exit 1
                '''
            }
        }
    }

    post {
        always {
            echo '🏁 Pipeline completed!'
            cleanWs()
        }
        success {
            echo '✅ Pipeline succeeded!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}
