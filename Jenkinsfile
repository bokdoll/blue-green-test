pipeline {
  agent any
  tools {nodejs "nodejs"}
  stages {
    stage('Source') {
      steps {
        git(url: 'https://github.com/bokdoll/blue-green-deployment-test.git', branch: 'master', poll: true, credentialsId: 'bokdoll')
      }
    }

    stage('Build') {
      steps {
        sh 'npm install'
        sh 'npm build'
      }
    }

    stage('S3 Upload'){
      steps{
        sh 'zip -rq bundle.zip .' // zip 파일 생성
        sh 'aws s3 cp ./bundle.zip s3://jenkins-beanstalk-deployment/application.zip --region ap-northeast-2' // s3로 bundle.zip 복사
      }
    }

    stage('Deploy') {
      steps {
        sh 'aws elasticbeanstalk create-application-version --region ap-northeast-2 --application-name blue-green-test --version-label app-ver4 --source-bundle S3Bucket="jenkins-beanstalk-deployment",S3Key="application.zip"'
        sh 'aws elasticbeanstalk update-environment --region ap-northeast-2 --environment-name blue-green-test-dev --version-label app-ver4'
        sh 'echo "Deploy Success"'
      }
    }

  }
}
