pipeline {
    agent any
    
    options {
        ansiColor('xterm')
    }

    tools {nodejs "NodeJS 16.13"}
    
    environment {
        STU_MGMT_SPEC = 'http://147.172.178.30:8080/stmgmt/api-json'
        EXERCISE_SUBMITTER_SPEC = 'https://jenkins-2.sse.uni-hildesheim.de/job/Teaching_exercise-submitter-server/lastSuccessfulBuild/artifact/target/openapi.json'
        SPARKY_SERVICE_SPEC = 'http://147.172.178.30:8080/v3/api-docs/'
        // STU_MGMT_SPEC = 'https://jenkins-2.sse.uni-hildesheim.de/job/Teaching_StudentMgmt-Backend/lastSuccessfulBuild/artifact/api-json'
    }
    
    stages {

        stage('Git') {
            steps {
                cleanWs()
                git 'https://github.com/Student-Management-System/api-client-generator.git'
            }
        }

        stage('Preparation') {
            steps {
                sh 'npm install @openapitools/openapi-generator-cli'
                sh 'chmod +x fetch-openapi generate remove-snapshot'
            }
        }
        
        stage('Student Mgmt') {
            when {
                expression { params.API == "STU-MGMT" }
            }
            steps {
                sh "./fetch-openapi student-mgmt ${env.STU_MGMT_SPEC}"
                sh './generate student-mgmt'
                
                // Based on: https://stackoverflow.com/a/58112719
                //           https://www.jenkins.io/doc/pipeline/steps/credentials-binding/
                withCredentials([string(credentialsId: 'NPM', variable: 'NPM_PUBLSH_TOKEN')]) {
                    sh 'rm -f ~/.npmrc'
                    sh 'echo //registry.npmjs.org/:_authToken=$NPM_PUBLSH_TOKEN >> ~/.npmrc'
                    dir('student-mgmt/generated/dist') {
                        sh 'npm publish --access public'
                    }
                }
            }
        }
        
        stage('Exercise Submitter') {
            when {
                expression { params.API == "EXERCISE-SUBMITTER" }
            }
            steps {
                sh "./fetch-openapi exercise-submitter-server ${env.EXERCISE_SUBMITTER_SPEC}"
                sh './generate exercise-submitter-server'
                
                // Based on: https://stackoverflow.com/a/58112719
                //           https://www.jenkins.io/doc/pipeline/steps/credentials-binding/
                withCredentials([string(credentialsId: 'NPM', variable: 'NPM_PUBLSH_TOKEN')]) {
                    sh 'rm -f ~/.npmrc'
                    sh 'echo //registry.npmjs.org/:_authToken=$NPM_PUBLSH_TOKEN >> ~/.npmrc'
                    dir('exercise-submitter-server/generated/dist') {
                        sh 'npm publish --access public'
                    }
                }
            }
        }
        
        stage('Sparky Service') {
            when {
                expression { params.API == "SPARKY" }
            }
            steps {
                sh "./fetch-openapi exercise-submitter-server ${env.SPARKY_SERVICE_SPEC}"
                sh './remove-snapshot sparky-service/openapi.json'
                sh './generate sparky-service'
                
                // Based on: https://stackoverflow.com/a/58112719
                //           https://www.jenkins.io/doc/pipeline/steps/credentials-binding/
                withCredentials([string(credentialsId: 'NPM', variable: 'NPM_PUBLSH_TOKEN')]) {
                    sh 'rm -f ~/.npmrc'
                    sh 'echo //registry.npmjs.org/:_authToken=$NPM_PUBLSH_TOKEN >> ~/.npmrc'
                    dir('sparky-service/generated/dist') {
                        sh 'npm publish --access public'
                    }
                }
            }
        }
        
        stage('Archives') {
            steps {
                sh 'wget $(npm view @student-mgmt/api-client dist.tarball)'
                sh 'wget $(npm view @student-mgmt/exercise-submitter-api-client dist.tarball)'
                sh 'wget $(npm view @student-mgmt/sparky-service dist.tarball)'
                archiveArtifacts artifacts: '*.tgz'
            }
        }
    }
}