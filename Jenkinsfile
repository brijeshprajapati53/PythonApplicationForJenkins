pipeline {
    agent any
    environment {
        AZURE_CREDENTIALS = credentials('azure-service-principal')
        RESOURCE_GROUP = 'myResourceGroup'
        APP_SERVICE_NAME = 'myPythonBrijesh002'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/brijeshprajapati53/PythonApplicationForJenkins.git'
            }
        }

        stage('Build') {
            steps {
                bat 'pip install -r requirements.txt'
            }
        }

        stage('Publish') {
            steps {
                bat 'powershell Compress-Archive -Path * -DestinationPath app.zip -Force'
            }
        }

        stage('Deploy to Azure') {
            steps {
                withCredentials([azureServicePrincipal(credentialsId: 'azure-service-principal')]) {
                    script {
                        def azureUser = env.AZURE_CREDENTIALS_USR
                        def azurePass = env.AZURE_CREDENTIALS_PSW
                        def azureTenant = env.AZURE_CREDENTIALS_TEN

                        if (!azureUser?.trim() || !azurePass?.trim() || !azureTenant?.trim()) {
                            error "Azure credentials are missing! Check Jenkins credentials."
                        }

                        bat """
                        az login --service-principal -u ${azureUser} -p ${azurePass} --tenant ${azureTenant}
                        az webapp deploy --resource-group ${RESOURCE_GROUP} --name ${APP_SERVICE_NAME} --src-path app.zip --type zip
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
