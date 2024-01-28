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

                   
                // setup project 
                sh 'cd /home/ec2-user'         
                sh 'cp "/tmp/jenkinsdir/workspace/Build Pipeline Industry Grade Project 1/target/ABCtechnologies-1.0.war" .'

                
                // Create a Dockerfile
                sh 'rm -rf Dockerfile'
                writeFile file: 'Dockerfile', text: '''
                    FROM tomcat:9.0.85-jdk8-corretto-al2
                    ADD . /usr/local/tomcat/webapps
                '''
          
     
                // Build Docker image
                sh 'docker build -t retailcart .'

                // Docker login
                withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB_CREDENTIALS', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                }
                // Tag Docker image
                sh 'docker tag retailcart richardtest123/retailcart:latest'
                
                // Push Docker image
                sh 'docker push richardtest123/retailcart:latest'

            }
        }

        stage('ansible') {
            agent { label 'master' }
            steps {
                // Invoke Ansible Playbook
               
                sh 'ansible-playbook play2.yml -f 2'
              
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


