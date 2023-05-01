pipeline {
  agent any
 
  tools {
  maven 'Maven3'
  }
  stages {
    stage ('Build') {
      steps {
      echo 'Get Git commit mesage'
      sh 'GIT_COMMIT_MSG=$(git log -1 --pretty=format:"%s")'
      }
    }
    stage ('Code Quality') {
      steps {
        echo 'Check if the commit message is "TEST-123'
        sh 'if [ "$GIT_COMMIT_MSG" == "TEST-123" ]; then
        echo "Commit message is 'TEST-123', proceeding with the build"
            else echo "Commit message is not 'TEST-123', aborting the buildd"
    exit 1
            fi'
        
      }
    }
    stage ('JaCoCo') {
      steps {
      jacoco()
      }
    }
    stage ('Nexus Upload') {
      steps {
      nexusArtifactUploader(
      nexusVersion: 'nexus3',
      protocol: 'http',
      nexusUrl: 'ec2-54-165-214-131.compute-1.amazonaws.com:8081',
      groupId: 'myGroupId',
      version: '1.0-SNAPSHOT',
      repository: 'maven-snapshots',
      credentialsId: 'Nexus',
      artifacts: [
      [artifactId: 'MyWebApp',
      classifier: '',
      file: 'MyWebApp/target/MyWebApp.war',
      type: 'war']
      ])
      }
    }
    stage ('DEV Deploy') {
      steps {
      echo "deploying to DEV Env "
      deploy adapters: [tomcat9(credentialsId: '173757b0-eaa2-4243-8268-21f7b336d357', path: '', url: 'http://ec2-54-86-60-19.compute-1.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
      }
    }
    stage ('Slack Notification') {
      steps {
        echo "deployed to DEV Env successfully"
        slackSend(channel:'devops-training', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }
    }
    stage ('DEV Approve') {
      steps {
      echo "Taking approval from DEV Manager for QA Deployment"
        timeout(time: 7, unit: 'DAYS') {
        input message: 'Do you want to deploy?', submitter: 'admin'
        }
      }
    }
     stage ('QA Deploy') {
      steps {
        echo "deploying to QA Env "
        deploy adapters: [tomcat9(credentialsId: '173757b0-eaa2-4243-8268-21f7b336d357', path: '', url: 'http://ec2-54-86-60-19.compute-1.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
        }
    }
    stage ('QA Approve') {
      steps {
        echo "Taking approval from QA manager"
        timeout(time: 7, unit: 'DAYS') {
        input message: 'Do you want to proceed to PROD?', submitter: 'admin,manager_userid'
        }
      }
    }
    stage ('Slack Notification for QA Deploy') {
      steps {
        echo "deployed to QA Env successfully"
        echo
        slackSend(channel:'devops-training', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }
    }  
  }
}