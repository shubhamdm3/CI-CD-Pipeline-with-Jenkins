pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                echo 'Building application...'
                // Add your build commands here
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests...'
                // Add your test commands here
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                // Add your deployment commands here
            }
        }
    }
}
stage('Test') {
    steps {
        echo 'Running unit tests...'
        sh 'mvn test'  // Example for Java/Maven
        echo 'Running integration tests...'
        sh 'mvn verify -DskipUnitTests'
        echo 'Running system tests...'
        sh './run-system-tests.sh'
    }
    post {
        always {
            junit 'target/test-reports/*.xml'
        }
    }
}
