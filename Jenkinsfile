pipeline {
    agent any

    environment {
        GIT_COMMIT_MSG = ""
    }

    options {
        skipDefaultCheckout()
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout scm
                    GIT_COMMIT_MSG = sh(script: 'git log -1 --pretty=format:"%s"', returnStdout: true).trim()
                }
            }
        }

        stage('Check Commit Message') {
            steps {
                script {
                    if (GIT_COMMIT_MSG == 'TEST-123') {
                        echo "Commit message is 'TEST-123', proceeding with the pipeline"
                    } else {
                        currentBuild.result = 'ABORTED'
                        error "Commit message is not 'TEST-123', aborting the pipeline"
                    }
                }
            }
        }

        stage('Your Job') {
            steps {
                echo 'Executing the main job'
                // Place your job steps here
            }
        }
    }
}
