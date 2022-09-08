pipeline {
    agent any
    parameters {
        choice(name: 'action', choices: 'create\ndestroy', description: 'Create/update or destroy the server')
        string(name: 'workspace', description: "Name of the workspace")
    }
    stages {
        stage('checkout') {
            steps {
                git 'https://github.com/vnarendra7/terr2.git'
            }
        }
        stage ('Approval') 
        {
            steps {
              script {
                timeout(time: 2, unit: 'HOURS') {
                input(id: "Deploy", message: "Deploy ${params.workspace}?", ok: 'Deploy')
                 }
              }
            }
        }
        stage('TF Plan') {
            when {
                expression { params.action == 'create' }
            }
            steps {
                script {
                        sh """
                        ls -l
                        pwd
                        hostname
                        terraform init
                        terraform workspace new ${params.workspace} || true
                        terraform workspace select ${params.workspace}
                        terraform plan
                        """
                    }
                }
        }
        stage('TF Apply') {
          when {
            expression { params.action == 'create' }
          }
          steps {
            script {
                        sh """ 
                        terraform apply -input=false -auto-approve
                        """
                    }
                }
        }
        stage('TF Destroy') {
          when {
            expression { params.action == 'destroy' }
          }
          steps {
            script {
                        sh """ 
                        terraform workspace select ${params.workspace}
                        terraform destroy -auto-approve
                        """
                    }
                }
        }
    }        
}
