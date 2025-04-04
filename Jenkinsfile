pipeline {
    agent any
    environment {
        AZURE_CREDENTIALS_ID = 'azure-service-principal'
        RESOURCE_GROUP = 'myResourceGroup'
        APP_SERVICE_NAME = 'myPythonBrijesh002'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/brijeshprajapati53/WebApplicationForJenkins.git'
            }
        }

        stage('Set Up Python') {
            steps {
                bat 'python -m venv venv'
                bat 'call venv\\Scripts\\activate && pip install -r requirements.txt'
            }
        }

       stage('Publish') {
            steps {
                bat '''
                powershell Compress-Archive -Path * -DestinationPath app.zip -Force
                '''
            }
        }

        
          stage('Deploy') {
            steps {
                withCredentials([azureServicePrincipal(credentialsId: AZURE_CREDENTIALS_ID)]) {
                    bat '''
                    if exist publish (rmdir /s /q publish)
                    mkdir publish

                    :: Copy .py files and requirements.txt to publish folder
                    for %%f in (*.py) do copy "%%f" publish\\
                    if exist requirements.txt copy requirements.txt publish\\
                    '''
                    bat 'az login --service-principal -u %AZURE_CLIENT_ID% -p %AZURE_CLIENT_SECRET% --tenant %AZURE_TENANT_ID%'
                    bat 'powershell Compress-Archive -Path ./publish/* -DestinationPath ./publish.zip -Force'
                    bat 'az webapp deploy --resource-group %RESOURCE_GROUP% --name %APP_SERVICE_NAME% --src-path ./publish.zip --type zip'
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
