pipeline{
    agent any
    
    environment {
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
        bat "docker build -t i-${username}-${env.BRANCH_NAME}:${BUILD_NUMBER} --no-cache -f TestWebApi/Dockerfile ."
        // bat "docker build . -t i_${username}_master"
    }
}

stage('Containers') {
            parallel {
                stage('Pre-Container Check') {
                    when {
                        expression {
                            return bat (script: "docker port i_${username}_${env.BRANCH_NAME}", returnStatus: true) == 0;
                        }
                    }
                    steps {
                        echo 'Stopping the already running container'
                        bat "docker rm -f i_${username}_${env.BRANCH_NAME}"
                    }
                }

stage('Move Image to Docker Hub'){
    steps{
        echo "Move Image to Docker Hub"
        //  bat "docker tag i_${username}_master ${registry}:${BUILD_NUMBER}"
          bat "docker tag i_${username}_${env.BRANCH_NAME}:${BUILD_NUMBER} ${registry}:${BUILD_NUMBER}"
        bat "docker tag i_${username}_${env.BRANCH_NAME}:${BUILD_NUMBER} ${registry}:latest"
        withDockerRegistry([credentialsId: 'DockerHub', url: '']){
            // bat "docker push ${registry}:${BUILD_NUMBER}"
             bat "docker push ${registry}:${BUILD_NUMBER}"
             bat "docker push ${registry}:latest"
        }
    }
}

stage('Docker Deployent'){
    steps{
        script{
             echo "Docker Deployment"
        echo "Starting the api container - docker"
         bat "docker run --name TestWebApi -d -p 7100:80 ${registry}:${BUILD_NUMBER}"
       // bat "docker run --name TestApi -d -p 7100:80 i_${username}_master"
       if (env.BRANCH_NAME == 'master') {
                        bat "docker run --name i_${username}_${env.BRANCH_NAME} -p ${masterAppPort}:${dockerPort} -d ${registry}:${BUILD_NUMBER}"    
                    }
                    else if (env.BRANCH_NAME == 'develop') {
                        bat "docker run --name i_${username}_${env.BRANCH_NAME} -p ${developAppPort}:${dockerPort} -d ${registry}:${BUILD_NUMBER}"
                    }
        }
       
    }
}
 }
}
