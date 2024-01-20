pipeline {
    tools {
        maven 'MyMaven'
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
        stage('UnitTesting') {
            agent { label 'slave01' }
            steps {
                sh 'mvn test'
            }
        }
        stage('Package') {
            agent { label 'slave01' }
            steps {
                sh 'mvn package'

                   
                // Create Dockerfile
                sh 'cd /home/ec2-user'
                sh 'rm -rf project'
                sh 'cd project'
                sh 'cp "/tmp/jenkinsdir/workspace/Build Pipeline Industry Grade Project 1/target/ABCtechnologies-1.0.war" .'

                
                // Create Dockerfile
                sh 'echo "FROM tomcat:9.0.85-jdk8-corretto-al2" > Dockerfile'
                sh 'echo "ADD . /usr/local/tomcat/webapps" >> Dockerfile'   
                 
                
                // Build Docker image
                sh 'docker build -t retailcart .'
                
                // Tag Docker image
                sh 'docker tag retailcart richardtest123/reatilcart:latest'
                
                // Push Docker image
                sh 'docker push richardtest123/reatilcart:latest'
            }
            post {
                success {
                    // Record the test results
                    junit '**/target/surefire-reports/TEST-*.xml'

                    // Archive the jar and Dockerfile
                    archiveArtifacts 'target/*.war'
                    archiveArtifacts 'Dockerfile'
                }
            }
        }
    }
}

