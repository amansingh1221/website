pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northeast-1:202051022'
       appRegistry = "964073772042.dkr.ecr.ap-northeast-1.amazonaws.com/202051022_capstone"
       capstoneRegistry = "https://964073772042.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "capstone"
        service = "202051022"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/amansingh1221/website'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: '202051022', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
