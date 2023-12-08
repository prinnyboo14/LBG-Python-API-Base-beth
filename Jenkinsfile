pipeline {
    agent any
    stages {
        stage('Initialise') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.154.0.45 << EOF
                docker stop flask-app || echo "flask-app not running"
                docker rm flask-app || echo "flask-app not running"
                '''
           }
        }
        stage('Build') {
            steps {
                sh '''
                docker build -t beth111/python-api -t beth111/python-api:v${BUILD_NUMBER} .   
                '''
           }
        }
        stage('Push') {
            steps {
                sh '''
                docker push beth111/python-api
                docker push beth111/python-api:v${BUILD_NUMBER}
                '''
           }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.154.0.45 << EOF
                docker run -d -p 80:8080 --name flask-app beth111/python-api
                '''
            }
        }
        stage('CleanUp') {
            steps {
                sh '''
                docker system prune -f
                '''
           }
        }
    }
}
