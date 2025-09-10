pipeline {
    agent any

    tools {
        maven 'Maven-3.9.11'   // Make sure this matches Jenkins Global Tool name
        jdk 'JDK-17'           // Make sure this matches Jenkins Global Tool name
    }

    environment {
        APP_NAME = "demo"
        JAR_FILE = "target/demo-0.0.1-SNAPSHOT.jar"
        DEPLOY_DIR = "${WORKSPACE}/deploy"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Yokesh103/SpringDev.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh "mvn test"
            }
        }

        stage('Deploy to Staging') {
            steps {
                sh """
                    echo "Stopping existing application if running..."
                    pkill -f ${APP_NAME} || true

                    echo "Preparing deployment directory..."
                    mkdir -p ${DEPLOY_DIR}

                    echo "Copying JAR to deploy directory..."
                    cp ${JAR_FILE} ${DEPLOY_DIR}/${APP_NAME}.jar

                    echo "Starting new application..."
                    nohup java -jar ${DEPLOY_DIR}/${APP_NAME}.jar --server.port=8080 > ${DEPLOY_DIR}/app.log 2>&1 &

                    echo "Waiting for application to start..."
                    sleep 20

                    echo "Testing application health..."
                    curl -f http://localhost:8080/actuator/health
                """
            }
        }
    }

    post {
        success {
            echo "✅ Build and deployment successful!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
