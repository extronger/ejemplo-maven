import groovy.json.JsonSlurperClassic
def jsonParse(def json) {
    new groovy.json.JsonSlurperClassic().parseText(json)
}

void setBuildStatus(String message, String context, String state) {
    withCredentials([string(credentialsId: 'github-commit-status-token', variable: 'TOKEN')]) {
        sh """
            set -x
            curl \"https://api.github.com/repos/org/repo/statuses/$GIT_COMMIT?access_token=$TOKEN\" \
                -H \"Content-Type: application/json\" \
                -X POST \
                -d \"{\\\"description\\\": \\\"$message\\\", \\\"state\\\": \\\"$state\\\", \\\"context\\\": \\\"$context\\\", \\\"target_url\\\": \\\"$BUILD_URL\\\"}\"
        """
    } 
}


pipeline {
    agent any
    stages {
        stage("Step 1: Compile code"){
            steps {
                script {
                sh "echo 'Compiling code'"
                sh "./mvnw clean compile -e"
                }
            }
        }
        stage("Step 2: Test code"){
            steps {
                script {
                sh "echo 'Testing code'"
                sh "./mvnw clean test -e"
                }
            }
        }
        stage("Step 3: Build .jar"){
            steps {
                script {
                sh "echo 'Building .jar'"        
                sh "./mvnw clean package -e"
                }
            }
        }
        stage("Step 4: Building .jar"){
            steps {
                script {
                sh "echo 'Building .jar'"
                sh "nohup bash mvnw spring-boot:run &"
                }
            }
        }
        stage("Step 5: Running microservice"){
            steps {
                script {
                sh "sleep 20"
                sh "curl -X GET http://localhost:8081/rest/mscovid/test?msg=ThisIsANewTestMessage"
                }
            }
        }
        stage('Last one') {
            steps {
                setBuildStatus("Compiling", "compile", "pending");
                script {
                    try {
                        // do the build here
                        setBuildStatus("Build complete", "compile", "success");
                    } catch (err) {
                        setBuildStatus("Failed", "pl-compile", "failure");
                        throw err
                    }
                }
            }
        }
    }
    post {
        always {
            sh "echo 'fase always executed post'"
        }
        success {
            sh "echo 'fase success'"
        }
        failure {
            sh "echo 'fase failure'"
        }
    }
}