#!groovy
pipeline{
    agent any
       
       stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/ryzhan/catalogue.git'
            }
        }   
        
        stage('Build catalogue') {
            steps {
                
                sh 'docker login -u _json_key -p "$(cat /var/lib/jenkins/credential/if-101-demo1-02c2a2eae285.json)" https://gcr.io'
                sh "docker build -t gcr.io/if-101-demo1/catalogue:3.0.0-$BUILD_NUMBER -t gcr.io/if-101-demo1/catalogue:latest \
                    -f docker/catalogue/Dockerfile ."
                sh "docker push gcr.io/if-101-demo1/catalogue"
                sh "docker rmi -f gcr.io/if-101-demo1/catalogue:latest gcr.io/if-101-demo1/catalogue:3.0.0-$BUILD_NUMBER"
               
 
                
            }
            
        }
           
        stage('Archive workspace') {
                steps {
                    archiveArtifacts artifacts: '**/*', fingerprint: true
                }
        }
        
        stage('Approve for Deploy') {
        
                steps {
                    timeout(time:5, unit:'DAYS') {
                        input message:'Approve deployment?'
                    }

                }
        }   
           
        stage('Run catalogue') {
            
            environment {
                DB_HOST = sh(script: "getent hosts db-server | cut -d' ' -f1", , returnStdout: true).trim()
            }
            
            steps {
                
                sh "git clone https://github.com/ryzhan/ConfDemo3.git"
                dir('./ConfDemo3/ansible'){
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
