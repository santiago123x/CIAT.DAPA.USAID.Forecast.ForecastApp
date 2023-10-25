// Define an empty map for storing remote SSH connection parameters
def remote = [:]

pipeline {

    agent any

    environment {
        user = credentials('docker_user')
        host = credentials('docker_host')
        name = credentials('docker_name')
        ssh_key = credentials('docker_devops')
    }

    stages {
        stage('Ssh to connect Docker server') {
            steps {
                script {
                    // Set up remote SSH connection parameters
                    remote.allowAnyHosts = true
                    remote.identityFile = ssh_key
                    remote.user = user
                    remote.name = name
                    remote.host = host
                    
                }
            }
        }
        stage('Deploy Aclimate ForecastApp') {
            steps {
                script {
                    sshCommand remote: remote, command: """
                        cd /opt/aclimate/aclimate_forecast_process/workdir/
                        cp -r forecast_app/* forecast_app_bk/
                        rm -r forecast_app/*
                        rm -fr releaseForecastApp.zip
                        curl -LOk https://github.com/santiago123x/CIAT.DAPA.USAID.Forecast.ForecastApp/releases/latest/download/releaseForecastApp.zip
                        unzip -o releaseForecastApp.zip
                        rm -fr releaseForecastApp.zip
                        cp -r publish/ForecastApp/* forecast_app/
                        rm -r forecast_app/appsettings.json
                        cp -r forecast_app_bk/appsettings.json forecast_app
                        rm -fr publish
                    """
                }
            }
        }
    }
    
    post {
        failure {
            script {
                    sshCommand remote: remote, command: """
                        cd /opt/aclimate/aclimate_forecast_process/workdir/
                        rm -fr forecast_app/*
                        cp -r forecast_app_bk/* forecast_app/
                    """
                }
            }
        }

        success {
            script {
                echo 'everything went very well!!'
            }
        }
    }
 
}