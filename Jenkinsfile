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
        stage('Start SonarQube Analysis'){
            steps{
                echo"start sonar qube analysis"
                withSonarQubeEnv('Test_Sonar'){
                    bat"${sonarScannerHome}\\SonarScanner.MSBuild.exe begin /k:Nagp_Exam /n:Nagp_Exam /v:1.0"
                }
            }
        }
          stage('Build the code'){
            steps{
                echo "Build the code step"
                bat"dotnet build -c release -o TestWebApi/app/build"
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
        stage('Build Docker Image'){
            steps{
                bat "dotnet clean"
                bat "docker build -t i_${username}_master : ${BUILD_NUMBER} --no-cache -f TestWebApi/Dockerfile ."
            }
        }
        stage('Push Image to dockerhub'){
            steps{
                echo"Pushing image to docker hub"
                bat "docker tag i_${username}_master : ${BUILD_NUMBER} ${registry} : ${BUILD_NUMBER}"
                bat "docker tag i_${username}_master : ${BUILD_NUMBER} ${registry : latest}"
                withDockerRegistry(credentialsID : 'Dockerhub', url : ''){
                    bat "docker push ${registry} : ${BUILD_NUMBER}"
                    bat "docker push ${registry} : latest"
                }
            }
    }
    }
}
