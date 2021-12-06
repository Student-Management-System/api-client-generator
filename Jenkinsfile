pipeline {
    agent any

    tools {nodejs "NodeJS 16.13"}
    
    environment {
        STU_MGMT_SPEC = 'https://jenkins-2.sse.uni-hildesheim.de/job/Teaching_StudentMgmt-Backend/lastSuccessfulBuild/artifact/api-json'
    }
    
    stages {

        stage('Git') {
            steps {
                cleanWs()
                git 'https://github.com/Student-Management-System/api-client-generator.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install @openapitools/openapi-generator-cli'
            }
        }

        stage('API: Student Mgmt') {
            steps {
                sh "fetch-openapi student-mgmt ${env.STU_MGMT_SPEC}"
                sh 'generate student-mgmt'
            }
        }
        
        stage('Publish: Student Mgmt') {
            when {
                expression { params.API == "STU-MGMT" }
            }
            steps {
                // TODO
                sh 'echo "Publish Student Mgmt API Client"'
            }
        }
    }
}