import groovy.json.JsonSlurperClassic
def jsonParse(def json) {
    new groovy.json.JsonSlurperClassic().parseText(json)
}
pipeline {
    agent any
    stages {
        stage("Step 1: Compile code"){
            steps {
                script {
                sh "echo 'Compiling code'"
                // Run Maven on a Unix agent.
                sh "./mvnw clean compile -e"
                }
            }
        }
        stage("Step 2: Test code"){
            steps {
                script {
                sh "echo 'Testing code'"
                // Run Maven on a Unix agent.
                sh "./mvnw clean test -e"
                }
            }
        }
        stage("Step 3: Build .jar"){
            steps {
                script {
                sh "echo 'Building .jar'"
                // Run Maven on a Unix agent.
                sh "./mvnw clean package -e"
                }
            }
        }
        stage("Step 4: Run .jar in local"){
            steps {
                script {
                sh "echo 'Running .jar'"            
                sh "./mvnw spring-boot:run"
                }
            }
        }
        stage("Step 5: Testing application"){
            steps {
                script {
                sh "echo 'Testing endpoint'"
                sh "curl -X GET 'http://localhost:8081/rest/mscovid/test?msg=testing'"
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