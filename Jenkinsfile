pipeline {
    agent {
        label 'agent007'
    }
    parameters {
        gitParameter branchFilter: 'origin/(.*)', defaultValue: 'master', name: 'BRANCH', type: 'PT_BRANCH'
    }
    stages {
        stage('Clone') {
            steps {
                git branch: "${params.BRANCH}", credentialsId: '9cda4691-5226-41b2-af25-9811ba725cab', url: 'https://gitlab.fbox.fpt.vn/team-api/ott.git'
            }
        }
        stage('Remove old package') {
            steps {
                sh 'rm -rf  src/Account/Account.API/out'
                sh 'rm -rf  src/APIDoc/out'
                sh 'rm -rf  src/Content/Content.API/out'
                sh 'rm -rf  src/Notification/Notification.API/out'
            }
        }
        stage('Build Account API') {
            steps {
                sh """
                #!/bin/bash
                cd src/Account/Account.API
                dotnet publish Account.API.csproj -c Release -o out
                """
            }
        }
        // stage('Publish') {
        //     steps {
        //         sh '/root/dotnet/dotnet publish ./src/Account/Account.API/Account.API.csproj -c Release -o ./src/Account/Account.API/out'
        //         sh '/root/dotnet/dotnet publish ./src/APIDoc/APIDoc.csproj -c Release -o ./src/APIDoc/out'
        //         sh '/root/dotnet/dotnet publish ./src/Content/Content.API/Content.API.csproj -c Release -o ./src/Content/Content.API/out'
        //         sh '/root/dotnet/dotnet publish ./src/Notification/Notification.API/Notification.API.csproj -c Release -o ./src/Notification/Notification.API/out'
        //     }
        // }
        stage('Test') {
            steps {
                slackSend color: 'good', message: "Deployment Successful !", channel: 'jenkins-notification', teamDomain: 'cicd-notifications', tokenCredentialId: 'slackbot'
            }
        }
    }
    post {
        failure {
            slackSend color: 'danger', message: "Deployment failed (<${env.BUILD_URL}|Open>)", channel: 'jenkins-notification', teamDomain: 'cicd-notifications', tokenCredentialId: 'slackbot'
        }
    }
}