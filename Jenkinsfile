pipeline {
  agent any

  environment {
    ARM_CLIENT_ID       = credentials('AZURE_CLIENT_ID')
    ARM_CLIENT_SECRET   = credentials('AZURE_CLIENT_SECRET')
    ARM_TENANT_ID       = credentials('AZURE_TENANT_ID')
    ARM_SUBSCRIPTION_ID = credentials('AZURE_SUBSCRIPTION_ID')
  }

  stages {

    stage('Install Terraform') {
      steps {
        bat '''
          echo Installing Terraform...
          curl -o terraform.zip https://releases.hashicorp.com/terraform/1.9.5/terraform_1.9.5_windows_amd64.zip
          tar -xf terraform.zip
          move terraform.exe C:\\Windows\\System32\\terraform.exe
          terraform --version
        '''
      }
    }

    stage('Checkout Code') {
      steps {
        git branch: 'main', url: 'https://github.com/tukus1963/terraform-jenkins-lab.git'
      }
    }

    stage('Terraform Init') {
      steps {
        bat 'terraform init'
      }
    }

    stage('Terraform Plan') {
      steps {
        bat '''
          terraform plan ^
            -var "subscription_id=%ARM_SUBSCRIPTION_ID%" ^
            -var "client_id=%ARM_CLIENT_ID%" ^
            -var "client_secret=%ARM_CLIENT_SECRET%" ^
            -var "tenant_id=%ARM_TENANT_ID%"
        '''
      }
    }

    stage('Manual Approval') {
      steps {
        input message: 'Approve deployment?', ok: 'Deploy'
      }
    }

    stage('Terraform Apply') {
      steps {
        bat '''
          terraform apply -auto-approve ^
            -var "subscription_id=%ARM_SUBSCRIPTION_ID%" ^
            -var "client_id=%ARM_CLIENT_ID%" ^
            -var "client_secret=%ARM_CLIENT_SECRET%" ^
            -var "tenant_id=%ARM_TENANT_ID%"
        '''
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
