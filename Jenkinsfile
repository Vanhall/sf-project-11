pipeline {
    agent any
    
    stages {
        stage('nginx') {
            steps {
                sh "docker run --name jenkins-nginx -d -p 9889:80 -v ${WORKSPACE}/sf-project-11:/usr/share/nginx/html nginx"
            }
        }
        stage('request') {
            steps {
                sleep(time: 5, unit: 'SECONDS')
                script {
                    def response = httpRequest 'http://158.160.86.42:9889'
                    if (response.status != 200) {
                        error("The server doesn't respond")
                    }
                }
            }
        }
        stage('md5') {
            steps {
                script {
                    def local_md5 = sh "md5sum ${WORKSPACE}/sf-project-11/index.html | awk '{ print \$1 }'"
                    def web_md5 = sh "curl -s http://158.160.86.42:9889/index.html | md5sum | awk '{ print \$1 }'"
                    if (local_md5 != web_md5) {
                        error("The md5 of index.html don't match")
                    }
                }
            }
        }
    }
    
    post {
        always {
            sh "docker rm -f jenkins-nginx"
            cleanWs()
        }
        failed {
            emailext body: ':(', subject: 'Build Failed', to: 'vasyapupkin@example.com'
        }
    }
}

