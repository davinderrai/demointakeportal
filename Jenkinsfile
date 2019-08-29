def app = 'UNKNOWN'
def IMAGE = 'UNKNOWN'
def VERSION = 'UNKNOWN'

pipeline {
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '10'))
    }

    environment {
        branch = 'master'
        credentialsIdentifier = 'Github'
        scmUrl = 'https://github.com/davinderrai/demointakeportal.git'
    }

    stages {
        stage('Checkout git') {
            steps {
                git(url: env.scmUrl, branch: env.branch, credentialsId: env.credentialsIdentifier, changelog: true)
                print env.credentialsIdentifier
                //bitbucketStatusNotify(buildState: "INPROGRESS")
            }
        }
        stage('Build') {
            steps {
                echo 'Clean and Compile the project'
				sh 'whoami'
                sh 'npm run build'
            }
        }
        stage('Test') {
            when {
                branch "master"
            }
            parallel {
                stage('Unit Test') {
                    steps {
                        echo "1:"
                        //sh 'mvn surefire:test surefire-report:report'
                        //emailext attachmentsPattern: '**/target/site/surefire-report.html', body: 'Find attachments', subject: 'test', to: 'simurgrai@gmail.com'
                    }
                }
                stage('Integration Test') {
                    steps {
                        echo "2:"
                        //sh 'mvn failsafe:integration-test failsafe:verify'
                    }
                }
            }
        }
        stage('Code Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    //sh 'mvn package -DskipTests sonar:sonar'
                }
            }
        }
        stage('Deploy') {
            steps {
                sh 'id'
                echo 'Deploy'
				sh 'npm run deploy'
            }
        }
  }

    post {
        always {
            // send build started notifications
            slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
        success {
            // send build started notifications
            slackSend (color: '#FFFF00', message: "Succeeded: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
            //bitbucketStatusNotify(buildState: "SUCCESSFUL")
        }
        unstable {
            slackSend (color: '#0000FF', message: "Unstable: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
           // bitbucketStatusNotify(buildState: "FAILED")
        }
        failure {
            slackSend (color: '#FF0000', message: "Failed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
           // bitbucketStatusNotify(buildState: "FAILED")
        }
    }
}