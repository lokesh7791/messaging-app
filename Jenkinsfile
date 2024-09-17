pipeline {
   agent any
    stages {
        stage('code analysis') {
           steps {
            echo 'CODE ANAYSIS STARTED'
            sh'cd frontend && sudo docker run --rm -e SONAR_HOST_URL="http://34.224.215.152:9000" -v ".:/usr/src" -e SONAR_TOKEN="sqp_c611c9fab1c5bd993b3681be2970d3168ae36af9" sonarsource/sonar-scanner-cli -Dsonar.projectKey=chat'
            echo 'CODE ANAYSIS FINISHED'
           } 
            
        }
        stage('BUILD APP') {
            steps {
                echo 'BUILD APP STARTED'
                sh ' cd frontend && npm install && npm run build'
                echo 'BUILD APP FINSIHED'
            }
        }
        stage('PUBLISH APP'){
            steps {
                script {
                    def packageJson = readJSON file: 'frontend/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "${packageJSONVersion}"
                    sh  "zip frontend/chat.${packageJSONVersion}.zip -r frontend/dist" 
                    sh "curl -v -u admin:12345 --upload-file latestfrontend/chat-${packageJSONVersion}.zip http://34.224.215.152:8081/repository/chat/"               }
            }
        }
        stage ('DEPLOY APP'){
            steps{
                script {
                    def packageJson = readJSON file: 'frontend/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "${packageJSONVersion}"
                    sh "curl -u admin:12345 -X GET \'http://34.224.215.152:8081/repository/chat/chat-${packageJSONVersion}.zip\' --output chat-'${packageJSONVersion}'.zip"
                    sh 'sudo rm -f /var/www/html/*'
                    sh "sudo unzip -o 'chat.${packageJSONVersion}'.zip"
                    sh "sudo cp -r frontend/dist/*  var/www/html " 

                }
            }
        }
        stage ('Clean UP Workspace'){
            steps {
                echo 'cleaning workspace'
                cleanWs()
            }
        }
    }
}