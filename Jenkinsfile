pipeline {
    agent any

    tools {
        maven 'Maven'   // Define in Jenkins Global Tool Config
        jdk 'Java'      // Define in Jenkins Global Tool Config
    }

    environment {
        SONARQUBE = credentials('sonar-token')   // Jenkins credential ID
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/<your-username>/amazon-jenkins-sonar.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy with Ansible') {
            steps {
                ansiblePlaybook(
                    playbook: 'ansible/deploy-tomcat.yml',
                    inventory: 'ansible/inventories/production'
                )
            }
        }
    }
}
