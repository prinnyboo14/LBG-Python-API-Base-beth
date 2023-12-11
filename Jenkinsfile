pipeline {
    agent any
    stages {
        stage('Initialise') {
            steps {
                    script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                            ssh -i ~/.ssh/id_rsa jenkins@10.154.0.45 << EOF

                            docker stop flask-app || echo "flask-app not running"
                            docker rm flask-app || echo "flask-app does not exist"
                            docker rmi beth/python-api || echo "Image does not exist"

                            docker stop nginx|| echo "nginx not running"
                            docker rm nginx || echo "nginx does not exist"
                            docker rmi beth/flask-nginx || echo "Image does not exist"

                            docker network create project || echo "Network already exists"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                            ssh -i ~/.ssh/id_rsa jenkins@10.154.0.56  << EOF

                            docker stop flask-app || echo "flask-app not running"
                            docker rm flask-app || echo "flask-app does not exist"
                            docker rmi beth/python-api || echo "Image does not exist"

                            docker stop nginx|| echo "nginx not running"
                            docker rm nginx || echo "nginx does not exist"
                            docker rmi beth/flask-nginx || echo "Image does not exist"

                            docker network create project || echo "Network already exists"
                        '''
                    } else {
                        sh '''
                        echo "Unrecognised branch"
                        '''
                    }
		        }
           }
        }
        stage('Build') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                            echo "Build not required in main"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                            docker build -t beth111/python-api -t beth111/python-api:v${BUILD_NUMBER} .   
                            docker build -t beth111/flask-nginx -t beth111/flask-nginx:v${BUILD_NUMBER} ./nginx 
                        '''
                    } else {
                        sh '''
                            echo "Unrecognised branch"
                        '''
                    }
		        }
           }
        }
        stage('Push') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                            echo "Build not required in main"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                            docker push beth111/python-api
                            docker push beth111/python-api:v${BUILD_NUMBER}

                            docker push beth111/flask-nginx
                            docker push beth111/flask-nginx:v${BUILD_NUMBER}
                        '''
                    } else {
                        sh '''
                            echo "Unrecognised branch"
                        '''
                    }
		        }
           }
        }
        stage('Deploy') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                            ssh -i ~/.ssh/id_rsa jenkins@10.154.0.45 << EOF
                            docker run -d --name flask-app --network project beth111/python-api
                            docker run -d -p 80:80 -- name nginx --network project beth111/flask-nginx
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                            ssh -i ~/.ssh/id_rsa jenkins@10.154.0.56  << EOF
                            docker run -d --name flask-app --network project beth111/python-api
                            docker run -d -p 80:80 -- name nginx --network project beth111/flask-nginx
                        '''
                    } else {
                        sh '''
                            echo "Unrecognised branch"
                        '''
                    }
		        }
                sh '''
                    ssh -i ~/.ssh/id_rsa jenkins@10.154.0.45 << EOF
                    docker run -d --name flask-app --network project beth111/python-api
                    docker run -d -p 80:80 -- name nginx --network project beth111/flask-nginx
                '''
            }
        }
        stage('CleanUp') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/dev') 
                    { 
                    sh '''
                        docker rmi beth111/python-api:v${BUILD_NUMBER}
                        docker rmi beth111/flask-nginx:v${BUILD_NUMBER}
                    '''
                    }
                }
                sh '''
                docker system prune -f
                '''
           }
        }
    }
}
