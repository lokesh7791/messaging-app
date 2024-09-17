pipeline {
   agent any
    stages {
        stage('code analysis') {
           steps {
            echo 'CODE ANAYSIS STARTED'
            sh'cd webapp && sudo docker run --rm -e SONAR_HOST_URL="http://34.224.215.15:9000" -v ".:/usr/src" -e SONAR_TOKEN="sqp_7563efbb2e089a1273e50cb7c07fed850865f157" sonarsource/sonar-scanner-cli -Dsonar.projectKey=chat'
            echo 'CODE ANAYSIS FINISHED'
           } 
            
        }
        stage('BUILD APP') {
            steps {
                echo 'BUILD APP STARTED'
                sh ' cd frontend && npm install && npn run build'
                echo 'BUILD APP FINSIHED'
            }
        }
        stage('PUBLISH APP'){
            steps {
                scrpit {
                    def packageJson = readJSON file: 'frontend/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "${packageJSONVersion}"
                    sh  "zip frontend/chat.${packageJSONVersion} -r frontend/dist" 
                    sh "curl -v -u admin:12345 --upload-file webapp/chat-${packageJSONVersion}.zip http://34.224.215.15:8081/repository/chat/"               }
            }
        }
        stage ('DEPLOY APP'){
            steps{
                scrpit {
                    def packageJson = readJSON file: 'frontend/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "${packageJSONVersion}"
                    sh "curl -u admin:12345 -X GET \'http://34.224.215.15:8081/repository/chat/chat-${packageJSONVersion}.zip\' --output chat-'${packageJSONVersion}'.zip"
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