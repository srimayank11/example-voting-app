Skip to content
Product
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
            sh 'cd vote && docker build -t 297931203145.dkr.ecr.us-east-1.amazonaws.com/demo2-n:${BUILD_NUMBER} .'
            sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 297931203145.dkr.ecr.us-east-1.amazonaws.com'
            sh 'docker push 297931203145.dkr.ecr.us-east-1.amazonaws.com/demo2-n:${BUILD_NUMBER}'
          }
        }
      }
    }
    stage('Deploy in ECS') {
      steps {
        script {
          sh'''
ECR_IMAGE="297931203145.dkr.ecr.us-east-1.amazonaws.com/demo2-n:${BUILD_NUMBER}"
TASK_DEFINITION=$(aws ecs describe-task-definition --task-definition ${TASK_FAMILY} --region ${AWS_DEFAULT_REGION})
NEW_TASK_DEFINTIION=$(echo $TASK_DEFINITION | jq --arg IMAGE "$ECR_IMAGE" '.taskDefinition | .containerDefinitions[0].image = $IMAGE | del(.taskDefinitionArn) | del(.revision) | del(.status) | del(.requiresAttributes) | del(.compatibilities) | del(.registeredAt) | del(.registeredBy)')
NEW_TASK_INFO=$(aws ecs register-task-definition --region ${AWS_DEFAULT_REGION} --cli-input-json "$NEW_TASK_DEFINTIION")
NEW_REVISION=$(echo $NEW_TASK_INFO | jq '.taskDefinition.revision')
aws ecs update-service --cluster ${CLUSTER} --service ${SERVICE_NAME} --task-definition ${TASK_FAMILY}:${NEW_REVISION} --region ${AWS_DEFAULT_REGION}'''
        }

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
    TASK_FAMILY = 'vote'
    CLUSTER = 'vote-appp'
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
