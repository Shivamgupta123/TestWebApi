pipeline{
    agent any
    environment {
        sonarScannerHome = tool name : 'sonar_scanner_dotnet'
    }
    stages  {
        stage('checkout'){
            steps{
                echo "Checkout code from git"
                checkout scm
            }
        }
        stage('Build the code'){
            steps{
                echo "Build the code step"
                bat"dotnet build -c release -o TestWebApi/app/build"
            }
        }
        stage('Start SonarQube Analysis'){
            steps{
                echo"start sonar qube analysis"
                withSonarQubeEnv('Test_Sonar'){
                    bat"${sonarScannerHome}\\SonarScanner.MSBuild.exe begin /k:Nagp_Exam /n:Nagp_Exam /v:1.0"
                }
            }
        }
         stage('Stop SonarQube Analysis'){
            steps{
                echo"start sonar qube analysis"
                withSonarQubeEnv('Test_Sonar'){
                    bat"${sonarScannerHome}\\SonarScanner.MSBuild.exe end"
                }
            }
        }
    }
}
