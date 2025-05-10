pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building application...'
                // Build commands for your application
                sh 'mvn clean compile'
            }
        }
        
        stage('Test') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        echo 'Running unit tests...'
                        sh 'mvn test'
                    }
                }
                stage('Integration Tests') {
                    steps {
                        echo 'Running integration tests...'
                        sh 'mvn verify -DskipUnitTests'
                    }
                }
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('Package') {
            steps {
                echo 'Packaging application...'
                sh 'mvn package -DskipTests'
                archiveArtifacts artifacts: 'target/*.war', fingerprint: true
            }
        }
        
        stage('Deploy to Staging') {
            steps {
                echo 'Deploying to staging environment...'
                // Staging deployment commands
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'Staging Server',
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'target/*.war',
                                    removePrefix: 'target',
                                    remoteDirectory: '/deploy/staging',
                                    execCommand: 'service tomcat restart'
                                )
                            ]
                        )
                    ]
                )
            }
        }
        
        stage('Integration Testing on Staging') {
            steps {
                echo 'Running tests on staging...'
                // Commands to test the staging deployment
            }
        }
        
        stage('Deploy to Production') {
            input {
                message "Deploy to production?"
                ok "Yes, deploy it!"
            }
            steps {
                echo 'Deploying to production environment...'
                // Production deployment commands
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'Production Server',
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'target/*.war',
                                    removePrefix: 'target',
                                    remoteDirectory: '/deploy/production',
                                    execCommand: 'service tomcat restart'
                                )
                            ]
                        )
                    ]
                )
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
            emailext (
                subject: "Pipeline Successful: ${currentBuild.fullDisplayName}",
                body: "The pipeline completed successfully.",
                recipientProviders: [[$class: 'DevelopersRecipientProvider']]
            )
        }
        failure {
            echo 'Pipeline failed!'
            emailext (
                subject: "Pipeline Failed: ${currentBuild.fullDisplayName}",
                body: "The pipeline failed. Please check the logs.",
                recipientProviders: [[$class: 'DevelopersRecipientProvider']]
            )
        }
    }
}
