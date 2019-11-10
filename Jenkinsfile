#!groovy
pipeline{
    agent any
       
       stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/ryzhan/ConfDemo3.git'
            }
        }   
        
        stage('Build front-end') {
            steps {
                
                dir('./ansible'){
                    sh 'ansible-playbook build_microservices.yml --tags "catalogue-build" --extra-var "BUILD_NUMBER=$BUILD_NUMBER WORKSPACE=$WORKSPACE"'
                }
                
            }
            
        }
        
        stage('Archive workspace') {
                steps {
                    archiveArtifacts artifacts: '**/*', fingerprint: true
                }
        }
           
        stage('Run front-end') {
            
            environment {
                DB_HOST = sh(script: "getent hosts db-server | cut -d' ' -f1", , returnStdout: true).trim()
            }
            
            steps {
                
                timeout(time:5, unit:'DAYS') {
                    input message:'Approve deployment?'
                }
                
                dir('./ansible'){
                    sh 'ansible-playbook build_microservices.yml --tags "catalogue-run" --extra-var "db_host=$DB_HOST"'
                }
                
            }
            
        }
        
        }
    
    post {
        always {
            echo 'I have finished'
            echo 'And cleaned workspace'
            deleteDir()
        }
        success {
            echo 'Job succeeeded!'
        }
        unstable {
            echo 'I am unstable :/'
        }
        failure {
            echo 'I failed :('
        }
        changed {
            echo 'Things were different before...'
        }
    }
}
