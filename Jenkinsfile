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
        sed -i '4s!/image: pankrys/wpdev:2!image: $registry:$BUILD_NUMBER' /home/pankrys/project/fine_sa/prod/docker-compose.yml
        exit
      ''')
      }
    }
  }
}
