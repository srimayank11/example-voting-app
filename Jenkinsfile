pipeline{
  agent {
    label 'worker'
  }
  options{
        buildDiscarder(logRotator(daysToKeepStr: '15'))
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
        retry(3)
    }
  parameters{
        string(name: 'BRANCH', defaultValue: 'main')
        booleanParam(name: 'UnitTestCases', defaultValue: false)
    }
  stages {
    stage('Build Docker Image') {
      parallel {
        stage('Build Docker Image') {
          steps {
            sh 'cd vote && docker build -t 297931203145.dkr.ecr.us-east-1.amazonaws.com/vote:${BUILD_NUMBER} .'
            sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 297931203145.dkr.ecr.us-east-1.amazonaws.com'
            sh 'docker push 297931203145.dkr.ecr.us-east-1.amazonaws.com/vote:${BUILD_NUMBER}'
          }
        }
      }
    }
    stage('Deploy in ECS') {
      steps {        

      }
    }
    stage('Adding test') {
      steps {
        sh 'echo separator'
      }
    }
  }
  environment {
    AWS_DEFAULT_REGION = 'us-east-1'
    SERVICE_NAME = 'vote'
    TASK_FAMILY = 'vote-fargate'
    CLUSTER = 'Jenkins-worker'
  }
  post{
        always{
            echo "I will run ALWAYS"
        }
        failure{
            echo "Only incase of FAILURES"
        }
    }
}
