pipeline {
    agent any

    parameters {
        booleanParam(
            name: 'autoApprove',
            defaultValue: false,
            description: 'Automatically approve apply/destroy?'
        )
        choice(
            name: 'action',
            choices: ['apply', 'destroy'],
            description: 'Select Terraform action'
        )
    }

    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_DEFAULT_REGION    = 'ap-south-1'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/cloudinstitution/terraform-modules.git'
            }
        }

        stage('Terraform Init') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'terraform init'
                    } else {
                        bat 'terraform init'
                    }
                }
            }
        }

        stage('Terraform Plan') {
            when {
                expression { params.action == 'apply' }
            }
            steps {
                script {
                    if (isUnix()) {
                        sh 'terraform plan -out=tfplan'
                        sh 'terraform show -no-color tfplan > tfplan.txt'
                    } else {
                        bat 'terraform plan -out=tfplan'
                        bat 'terraform show -no-color tfplan > tfplan.txt'
                    }
                }
            }
        }

        stage('Apply / Destroy') {
            steps {
                script {
                    def approveFlag = params.autoApprove ? '--auto-approve' : ''

                    if (isUnix()) {
                        sh "terraform ${params.action} ${approveFlag}"
                    } else {
                        bat "terraform ${params.action} ${approveFlag}"
                    }
                }
            }
        }
    }
}
