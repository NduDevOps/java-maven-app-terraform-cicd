#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
  [$class: 'GitSCMSource',
  remote: 'https://github.com/OkomaNdu/Jenkins-Shared-Library.git',
  credentialsId: 'github-credentials'
  ]
)

pipeline {   
  agent any
  tools {
    maven 'maven-3.9'
  }
  environment {
    IMAGE_NAME = 'ndubuisip/demo-app:java-maven-2.0'
  }
  stages {
    stage("build app") {
      steps {
        script {
          echo 'building application jar...'
          buildJar()
        }
      }
    }
    stage("build image") {
      steps {
        script {
          echo 'building docker image...'
          buildImage(env.IMAGE_NAME)
          dockerLogin()
          dockerPush(env.IMAGE_NAME)
        }
      }
    }
    stage("deploy") {
      steps {
        script {
          echo 'deploying docker image to EC2...'
          
          def shellCmd = "bash ./server-cmds.sh ${IMAGE_NAME}"
          def ec2Instance = "ec2-user@$35.180.151.121"

          sshagent(['server-ssh-key']) {
            sh "scp -o server-cmds.sh ${ec2Instance}:/home/ec2-user"
            sh "scp -o docker-compose.yaml ${ec2Instance}:/home/ec2-user"
            sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${shellCmd}"
          }
        }
      }
    }               
  }
}
