pipeline {
  agent any
  stages {
    stage ('Build') {
      steps {
        sh 'printenv'
        sh 'docker build -t dockerimage:""$GIT_COMMIT"" .'
      }
    }
    
     stage ('Publish to DockerHub') {
      steps {
        withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
          sh 'docker push dockerimage:""$GIT_COMMIT""'
         }
       }
     }
    stage ('Publish to ECR') {
      steps {
        //sh 'aws ecr-public get-login-password --region eu-west-2 | docker login --username AWS --password-stdin public.ecr.aws/f4z4b0p9'
        //withAWS(credentials: 'sam-jenkins-demo-credentials', region: 'us-east-1') {
         withEnv(["AWS_ACCESS_KEY_ID=${env.AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${env.AWS_SECRET_ACCESS_KEY}", "AWS_DEFAULT_REGION=${env.AWS_DEFAULT_REGION}"]) {
          sh 'docker login -u AWS -p $(aws ecr-public get-login-password --region us-east-1) public.ecr.aws/f4z4b0p9' 
          sh 'docker build -t dockerimage .'
          sh 'docker tag dockerimage:latest public.ecr.aws/f4z4b0p9/dockerimage:latest'
          sh 'docker push public.ecr.aws/f4z4b0p9/dockerimage:latest'
         }
       }
    }
  }
}
