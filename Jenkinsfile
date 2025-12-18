pipeline {
    agent any

    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
        choice(name: 'action', choices: ['apply', 'destroy'], description: 'Select the action to perform')
    }
    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_DEFAULT_REGION    = 'ap-south-1'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/cloudinstitution/terraform-modules.git'
            }
        }
        stage('Terraform init') {
            steps {
              if (isUnix()) {
                sh 'terraform init'
              } else {
                bat 'terraform init'
              }
            }
        }
        stage('Plan') {
            steps {
              if (isUnix()) {
                sh 'terraform plan -out tfplan'
                sh 'terraform show -no-color tfplan > tfplan.txt'
             } else {
                bat 'terraform plan -out tfplan'
                bat 'terraform show -no-color tfplan > tfplan.txt'
              }
            }
        }
        stage('Apply / Destroy') {
            steps {
                script {
              if (isUnix()) {
                   sh 'terraform ${action} --auto-approve'
              } 
              else {
                   bat 'terraform ${action} --auto-approve'
              }
                }
            }
        }
    }
}
