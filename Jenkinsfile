pipeline {
    agent any

    tools {
        maven 'Maven'   // 🔥 THIS LINE FIXES YOUR ISSUE
    }

    environment {
        USER_IMAGE = "user-service"
        BOOK_IMAGE = "book-service"
        ORDER_IMAGE = "order-service"
    }

    stages {

        stage('Build Services (Maven)') {
            steps {
                sh '''
                mvn -version
                cd user-service && mvn clean package -DskipTests
                cd ../book-service && mvn clean package -DskipTests
                cd ../order-service && mvn clean package -DskipTests
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                docker build -t $USER_IMAGE ./user-service
                docker build -t $BOOK_IMAGE ./book-service
                docker build -t $ORDER_IMAGE ./order-service
                '''
            }
        }

        stage('Cleanup Old Containers') {
            steps {
                sh '''
                docker stop user || true
                docker rm user || true
                docker stop book || true
                docker rm book || true
                docker stop order || true
                docker rm order || true
                '''
            }
        }

        stage('Run Containers') {
            steps {
                sh '''
                docker run -d -p 8081:8080 --name user $USER_IMAGE
                docker run -d -p 8082:8080 --name book $BOOK_IMAGE
                docker run -d -p 8083:8080 --name order $ORDER_IMAGE
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment Successful 🚀"
        }
        failure {
            echo "Pipeline Failed ❌"
        }
    }
}
