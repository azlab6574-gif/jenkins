pipeline {
    agent any

    environment {
        ARM_CLIENT_ID       = credentials('AZURE_CLIENT_ID')
        ARM_CLIENT_SECRET   = credentials('AZURE_CLIENT_SECRET')
        ARM_TENANT_ID       = credentials('AZURE_TENANT_ID')
        ARM_SUBSCRIPTION_ID = credentials('AZURE_SUBSCRIPTION_ID')
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/azlab6574-gif/jenkins.git'
            }
        }

        stage('Terraform Init') {
            steps {
                powershell '''
                    terraform init
                '''
            }
        }

        stage('Terraform Plan') {
            steps {
                powershell """
                    terraform plan `
                      -var subscription_id=$env:ARM_SUBSCRIPTION_ID `
                      -var client_id=$env:ARM_CLIENT_ID `
                      -var client_secret=$env:ARM_CLIENT_SECRET `
                      -var tenant_id=$env:ARM_TENANT_ID
                """
            }
        }

        stage('Manual Approval') {
            steps {
                input message: 'Approve deployment?', ok: 'Deploy'
            }
        }

        stage('Terraform Apply') {
            steps {
                powershell """
                    terraform apply -auto-approve `
                      -var subscription_id=$env:ARM_SUBSCRIPTION_ID `
                      -var client_id=$env:ARM_CLIENT_ID `
                      -var client_secret=$env:ARM_CLIENT_SECRET `
                      -var tenant_id=$env:ARM_TENANT_ID
                """
            }
        }
    }

    post {
        success {
            echo '✅ Infrastructure provisioned successfully!'
        }
        failure {
            echo '❌ Build failed. Check logs.'
        }
    }
}
