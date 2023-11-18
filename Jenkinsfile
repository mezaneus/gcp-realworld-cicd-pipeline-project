pipeline {
    agent any
  
    stages {
        stage('Validate Project') {
            steps {
                sh 'mvn validate'
            }
        }
        stage('Unit Test'){
            steps {
                sh 'mvn test'
            }
        }
        stage('Integration Test'){
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }
        stage('App Packaging'){
            steps {
                sh 'mvn package'
            }
        }
        stage ('Checkstyle Code Analysis'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('SonarQube Inspection') {
            steps {
                sh  """mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=java-webapp-project \
                        -Dsonar.host.url=http://10.128.0.19:9000 \
                        -Dsonar.login=5447c707a3c669dc9e1c59d124c5df50ff02af4d"""
            }
        }
        stage("Upload Artifact To Nexus"){
            steps{
                 sh 'mvn deploy'
            }
            post {
                success {
                  echo 'Successfully Uploaded Artifact to Nexus Artifactory'
                }
            }
        }
    }
    post {
    always {
        echo 'Slack Notifications.'
        slackSend channel: '#mike-jenkins-ci-pipeline', //update and provide your channel name
        color: COLOR_MAP[currentBuild.currentResult],
        message: "*${currentBuild.currentResult}:* Job Name '${env.JOB_NAME}' build ${env.BUILD_NUMBER} \n Build Timestamp: ${env.BUILD_TIMESTAMP} \n Project Workspace: ${env.WORKSPACE} \n More info at: ${env.BUILD_URL}"
    }
  }
}
