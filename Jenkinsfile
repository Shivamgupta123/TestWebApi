pipeline{
    agent any
    
    environment {
        scannerHome = tool name: 'sonar_scanner_dotnet'
        dotnet ='C:\\Program Files (x86)\\dotnet\\'
        registry = 'shivamgupta04/samplewebapi'
        username = 'shivamgupta'
        registryCredential = 'DockerHub' 
        dockerImage = '' 
        masterAppPort = 7200
        developAppPort = 7300
        dockerPort = 7100
        masterAppName = 'dotnet-app-master-deployment'
        masterServiceName = 'dotnet-app-master'
        masterExposedPort = 30157
        developAppName = 'dotnet-app-develop-deployment'
        developServiceName = 'dotnet-app-develop'
        developExposedPort = 30158
        }
        
        stages{
 stage('Checkout') {
    steps {
         checkout scm
     }
  }
   stage('Nuget Restore'){
   steps{
       echo "Noget Restore step"
       bat "dotnet restore"
      }
   }
stage('Start SonarQube Analysis') { 
steps {
echo "Start sonarQube Analysis Step"
withSonarQubeEnv('Test_Sonar') {
bat "${scannerHome}\\SonarScanner.MSBuild.exe begin /k:test /n:sonar_shivam /v:1.0"
}
}
}
   stage('Code Build'){
   steps{
       echo "Clean previous build"
       bat "dotnet clean"
       echo "Code Build"
      // bat "dotnet build ${workspace}\\TestWebApi.csproj --configuration Release"
      bat "dotnet build -c Release -o TestWebApi/app/build"

     }
  }
stage('Docker Image'){
    steps{
        echo "Docker Image Step"
        bat "dotnet publish -c Release"
        // bat "docker build -t i_${username}_master --no-cache -f Dockerfile ."
        bat "docker build -t i_${username}_master --no-cache -f TestWebApi/Dockerfile ."
        // bat "docker build . -t i_${username}_master"
    }
}

stage('Move Image to Docker Hub'){
    steps{
        echo "Move Image to Docker Hub"
         bat "docker tag i_${username}_master ${registry}:${BUILD_NUMBER}"
        withDockerRegistry([credentialsId: 'DockerHub', url: '']){
            bat "docker push ${registry}:${BUILD_NUMBER}"
        }
    }
}

stage('Docker Deployent'){
    steps{
        echo "Docker Deployment"
         bat "docker run --name TestWebApi -d -p 7100:80 ${registry}:${BUILD_NUMBER}"
       // bat "docker run --name TestApi -d -p 7100:80 i_${username}_master"
    }
}
 }
}
