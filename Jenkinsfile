pipeline {
    agent any

    environment {
        GIT_COMMIT_MSG = ""
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh '''
                    GIT_COMMIT_MSG=$(git log -1 --pretty=format:"%s")
                    echo "GIT_COMMIT_MSG=$GIT_COMMIT_MSG" > commit_msg.env
                '''
                script {
                    env.GIT_COMMIT_MSG = readFile('commit_msg.env').trim().split('=')[1]
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
