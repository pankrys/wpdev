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
        whoami
        #sed '4s,pankrys/wpdev:2,"$registry:$BUILD_NUMBER"' /home/pankrys/project/fine_sa/prod/docker-compose.yml
        docker stop prod_wordpress_1 prod_db_1 dev_wordpress_1 dev_db_1
        cp -r /home/pankrys/project/fine_sa/dev/mysql/wordpress /home/pankrys/project/fine_sa/prod/mysql/wordpress
        cd /home/pankrys/project/fine_sa/dev && docker-compose up -d
        cd /home/pankrys/project/fine_sa/prod && docker-compose up -d
        exit
      ''')
      }
    }
  }
}
