pipeline {
    agent any

    tools {
         maven 'Maven-3.8.1'  // configure in Jenkins → Global Tool Configuration
         jdk 'JDK-17'            // configure JDK 17 in Jenkins
    }

    environment {
        APP_NAME = "demo"
        JAR_FILE = "target/demo-0.0.1-SNAPSHOT.jar"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Yokesh103/SpringDev.git'
            }
        }

       stage('Build with Maven') {
    steps {
        sh '''
        mvn clean package -DskipTests
        echo "=== Contents of target/ after build ==="
        ls -lh target/
        '''
    }
}

        stage('Run Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy to Staging') {
    steps {
        sh '''
        echo "Stopping existing application if running..."
        pkill -f $APP_NAME || true

        sh '''
  echo "Copying JAR to workspace deployment folder..."
  mkdir -p $WORKSPACE/deploy
  cp target/demo-0.0.1-SNAPSHOT.jar $WORKSPACE/deploy/
  nohup java -jar $WORKSPACE/deploy/demo-0.0.1-SNAPSHOT.jar --server.port=8080 &
'''


        echo "Starting new application..."
        nohup java -jar /opt/apps/$APP_NAME.jar --server.port=8080 > /opt/apps/app.log 2>&1 &

        echo "Waiting for application to start..."
        sleep 20

        echo "Testing application health..."
        curl -f http://localhost:8080/  || exit 1
        '''
    }
}

        stage('Integration Tests') {
            steps {
                sh '''
                echo "Testing application endpoints..."
                # adjust /hello if your app uses a different path
                curl -f http://localhost:8080/hello || exit 1
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline execution completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
