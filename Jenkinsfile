pipeline {
  agent any
  stages {
    stage('Create Network') {
      steps {
        sh 'docker network create notejam'
      }
    }

    stage('Start Database') {
      steps {
        sh '''



docker run -d --network notejam -p 5432:5432 -e POSTGRES_PASSWORD=GameON123 -e POSTGRES_DB=notejam --name postgres-con postgres:8.4.22'''
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker build -t voltronops/notejam:3.0 .'
      }
    }

    stage('Start Notejam') {
      steps {
        sh 'docker run -d --network notejam -p 8000:8000 --name notejam-con voltronops/notejam:3.0'
      }
    }

    stage('Run Tests') {
      steps {
        sh 'docker exec  notejam-con python manage.py test'
      }
    }

    stage('Post Run') {
      steps {
        sh 'docker stop notejam-con'
        sh 'docker stop postgres-con'
        sh 'docker rm notejam-con'
        sh 'docker rm postgres-con'
        sh 'docker network remove notejam'
      }
    }

  }
}