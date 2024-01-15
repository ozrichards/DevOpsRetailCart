pipeline {
       
    tools {
        maven 'Mymaven'
    }
    agent none
    stages {
        stage('Clonerepo') {
            agent { label 'slave01' }
            steps {
                git 'https://github.com/ozrichards/DevOpsRetailCart.git'
            }
        }
        stage('Compile') {
            agent { label 'slave01' }
            steps {
                sh 'mvn compile'
            }
        }
        stage(UnitTesting){
            agent { label 'slave01' }
            steps {
                sh 'mvn test'
            }
        }
        stage('Package'){
            agent any
            steps {
               sh 'mvn package'   
            }
            post {
                success {
                    // Record the test results
                    junit '**/target/surefire-reports/TEST-*.xml'
                    
                    // Archive the jar
                    archiveArtifacts 'target/*.war'
                }
            }
        }
    }
}
