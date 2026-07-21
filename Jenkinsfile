pipeline {
    agent any
    environment{
        CHROME_VERSION = '127.0.6533.73'
        CHROMEDRIVER_VERSION = '127.0.6533.72'
        CHROME_INSTALL_PATH = 'C:\\Program Files\\Google\\Chrome\\Application'
        CHROMEDRIVER_PATH = 'C:\\Program Files\\Google\\Chrome\\Application\\chromedriver.exe'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/d1v1d3/Selenium-IDE.git'
            }
        }
        stage('.NET Core Setup') {
            steps {
                bat 'choco install dotnet-sdk -y --version=6.0.100' 
            }
        }
        stage('Uninstall Chrome') {
            steps {
                bat 'choco uninstall googlechrome -y' 
            }
        }
        stage('Install Specific Chrome Version') {
            steps {
                bat 'choco install googlechrome --version=%CHROME_VERSION% -y --allow-downgrade --ignore-checksums' 
            }
        }
        stage('Download and Install ChromeDriver') {
            steps {
                bat '''
                echo Downloading ChromeDriver version %CHROMEDRIVER_VERSION%
                powershell -command "Invoke-WebRequest -Uri https://chromedriver.storage.googleapis.com/%CHROMEDRIVER_VERSION%/chromedriver_win32.zip -OutFile chromedriver.zip -UseBasicParsing"
                powershell -command "Expand-Archive -Path chromedriver.zip -DestinationPath ."
                powershell -command "Move-Item -Path .\\chromedriver.exe -Destination '%CHROME_INSTALL_PATH%\\chromedriver.exe' -Force"
                '''
            }
        }
        stage('Restore') {
            steps {
                bat 'dotnet restore'
            }
        }
        stage('Build') {
            steps {
                bat 'dotnet build --no-restore'
            }
        }
        stage('Test') { 
            steps {
                bat 'dotnet test --no-build --no-restore'
            }
            post{
                always{
                    archiveArtifacts artifacts: '**/TestResults/*.trx', allowEmptyArchive: true
                    junit '**/TestResults/*.trx'
                }
            }
        }
        
    }
}