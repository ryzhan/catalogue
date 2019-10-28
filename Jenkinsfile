#!groovy
// Check properties
properties([disableConcurrentBuilds()])

pipeline {
        agent {
        label 'master'
        }
        
        stages {
        stage('Preparation') {
            steps {
                    git 'https://github.com/ryzhan/catalogue.git'
            }
        }
        
        stage('Build app') {
                
                environment {
                    DOCKER_HOST="ssh://jenkins@app-server"
                }
            
                steps {
                    sh 'docker build --no-cache -t catalogue -f docker/catalogue/Dockerfile .' 
                }
        }
        
       stage('Deploy') {
            environment {
                DB_NETWORK_IP = sh(script: "cat /var/lib/jenkins/db_local_ip", , returnStdout: true).trim()
                CHECK_CONTAINER = sh(script: "ssh -oStrictHostKeyChecking=no jenkins@app-server /opt/check-catalogue.sh", , returnStdout: true).trim()
                DOCKER_HOST="ssh://jenkins@app-server"
            }
                
            steps {
                
                timeout(time:5, unit:'DAYS') {
                    input message:'Approve deployment?', submitter: 'it-ops'
                }
                
                script {
                    
                    if ("${CHECK_CONTAINER}" == 'catalogue') {
                        sh "docker restart catalogue"
                        
                    } else {
                        sh "docker run -d --name catalogue --restart always --add-host catalogue-db:${DB_NETWORK_IP} --network socks -p 8080:80 catalogue"
                    }
                    //sh 'docker start catalogue' 
                }
                
                
                    
            }
        }
        
        stage('Archive') {
                steps {
                    archiveArtifacts artifacts: '**/*', fingerprint: true
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
