pipeline {
    agent any
    
    options {
        ansiColor('xterm')
    }

    tools {nodejs "NodeJS 16.13"}
    
    environment {
        STU_MGMT_SPEC = 'http://147.172.178.30:8080/stmgmt/api-json'
        // STU_MGMT_SPEC = 'https://jenkins-2.sse.uni-hildesheim.de/job/Teaching_StudentMgmt-Backend/lastSuccessfulBuild/artifact/api-json'
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

        stage('API Generation') {
            steps {
                sh 'chmod +x fetch-openapi generate'
                sh "./fetch-openapi student-mgmt ${env.STU_MGMT_SPEC}"
                sh './generate student-mgmt'
            }
        }
        
        stage('Publish: Student Mgmt') {
            when {
                expression { params.API == "STU-MGMT" }
            }
            steps {
                // Based on: https://stackoverflow.com/a/58112719
                //           https://www.jenkins.io/doc/pipeline/steps/credentials-binding/
                withCredentials([string(credentialsId: 'NPM', variable: 'NPM_PUBLSH_TOKEN')]) {
                    sh 'rm -f .npmrc'
                    sh 'echo //registry.npmjs.org/:_authToken=$NPM_PUBLSH_TOKEN >> .npmrc'
                    dir('student-mgmt/generated/dist') {
                        sh 'npm publish --access public'
                    }
                }
            }
        }
    }
}