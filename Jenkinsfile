pipeline {
    agent any

    environment {
        DEPLOY_DIR = "${WORKSPACE}/deploy"
        JAR_NAME = "demo.jar"
    }

    stages {

        stage('Checkout SCM') {
            steps {
                git url: 'https://github.com/Yokesh103/SpringDev.git', branch: 'master'
            }
        }

        stage('Build with Maven') {
            tools {
                maven 'Maven-3.9.11' // Replace with your Maven tool name in Jenkins
                jdk 'JDK17'           // Replace with your JDK tool name in Jenkins
            }
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Run Unit Tests') {
            tools {
                maven 'Maven-3.9.11'
                jdk 'JDK17'
            }
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy to Staging') {
            steps {
                sh '''
                    echo "Stopping existing application if running..."
                    pkill -f demo || true

                    echo "Preparing deployment directory..."
                    mkdir -p $DEPLOY_DIR

                    echo "Copying JAR to deploy directory..."
                    cp target/demo-0.0.1-SNAPSHOT.jar $DEPLOY_DIR/$JAR_NAME

                    echo "Starting new application..."
                    nohup java -jar $DEPLOY_DIR/$JAR_NAME --server.port=8080 &

                    echo "Waiting for application to start..."
                    sleep 20

                    echo "Testing application health..."
                    curl -f http://localhost:8080/actuator/health
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
