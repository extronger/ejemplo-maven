def msgCommon = "[Diego Inostroza][${env.JOB_NAME}][${env.BUILD_NUMBER}]"
def failedStage

pipeline {
    agent any
    stages {
        stage('Paso 1: Compilar') {
            steps {
                script {
                    failedStage = env.STAGE_NAME
                    sh './mvnw clean compile -e'
                }
            }
        }
        stage('Paso 2: Testear') {
            steps {
                script {
                    failedStage = env.STAGE_NAME
                    sh './mvnw clean test -e'
                }
            }
        }
        stage('Paso 3: Análisis SonarQube') {
            steps {
                script {
                    failedStage = env.STAGE_NAME
                    error('Test')
                }
                withSonarQubeEnv('sonarqube') {
                    sh './mvnw clean verify sonar:sonar -Dsonar.projectKey=custom-project-key'
                }
            }
        }
        stage('Paso 4: Build .Jar') {
            steps {
                script {
                    failedStage = env.STAGE_NAME
                    sh './mvnw clean package -e'
                }
            }
        }
        stage('Step 5: Running microservice') {
            steps {
                script {
                    failedStage = env.STAGE_NAME
                    sh('''
                        nohup bash mvnw spring-boot:run &
                        sleep 10
                        curl -X GET "http://localhost:8081/rest/mscovid/test?msg=ThisIsANewTestMessage"
                ''')
                }
            }
        }
    }
    post {
        success {
            slackSend color: 'good',
            message: "${msgCommon} Ejecucion Exitosa",
            teamDomain: 'devopsusach20-lzc3526',
            tokenCredentialId: 'slack-token-my-channel'
        }
        failure {
            slackSend color: 'danger',
            message: "${msgCommon} Ejecucion fallida en stage [${failedStage}]",
            teamDomain: 'devopsusach20-lzc3526',
            tokenCredentialId: 'slack-token-my-channel'
        }
    }
}
