pipeline{
    agent any
    stages{
        stage('Checkout Source Code'){
            steps{
                script{
                    checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/voltron-ops/notejam.git']]])
                }
            }
        }
        stage('Test Source Code with Database'){
            steps{
                script{
                    def postgres = docker.image('postgres').run('-e "POSTGRES_PASSWORD=GameON123" -e "POSTGRES_DB=notejam" -p 5432:5432')
                    def notejam = docker.image('voltronops/python2').inside("--link ${postgres.id}:postgres") {
                        sh 'python --version'
                        sh 'python manage.py syncdb --noinput'
                        sh 'python manage.py migrate'
                        sh 'python manage.py test'
                    }
                    postgres.stop()
                }
            }
        }
        stage('Build and Push Docker Image'){
            steps{
                script{
                    def customImage = docker.build("voltronops/notejam:${env.BUILD_ID}", '.')
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerID' ){
                        customImage.push()
                    }
                }
            }
        }
    }
    post{
        failure{
            sh 'docker stop $(docker ps -a -q)'
            sh 'docker rm $(docker ps -a -q)'
        }
    }
}

// This is just for testing purpose
