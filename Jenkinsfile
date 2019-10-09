pipeline {
  environment {
    registry = "pankrys/wpdev"
    registryCredential = 'dockerhub'
  }
  agent any
  stages {
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
    stage('Update version') {
      steps{
        sh('''#!/bin/bash
        echo "update $registry:$BUILD_NUMBER"
        ssh pankrys@192.168.113.36
        cd ~/project/fine_sa/prod/
        sed -i -e "s/image: pankrys/wpdev:2/image: $registry:$BUILD_NUMBER"
        exit
      ''')
      }
    }
  }
}
