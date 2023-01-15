pipeline{
    agent any
//    agent {label 'worker'}
    options{
        buildDiscarder(logRotator(daysToKeepStr: '7'))
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
        retry(3)
    }
    parameters{
        string(name: 'BRANCH', defaultValue: 'master')
        booleanParam(name: 'UnitTestCases', defaultValue: false)
        choice(name: 'Environment', choices: ['Dev', 'QA', 'Uat', 'Prod'])
    }
    triggers{
        cron('H */4 * * *')
        pollSCM('H */4 * * *')
    }
    stages{
        stage("First Stage"){
            steps{
                cd vote
                aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 297931203145.dkr.ecr.us-east-1.amazonaws.com
            }
        }
        stage("Second Stage"){
            steps{
                docker build -t 297931203145.dkr.ecr.us-east-1.amazonaws.com/vote:v${BUILD_NUMBER} .
                docker push 297931203145.dkr.ecr.us-east-1.amazonaws.com/vote:v${BUILD_NUMBER} 
                ECR_IMAGE="297931203145.dkr.ecr.us-east-1.amazonaws.com/vote:v${BUILD_NUMBER}" 
                TASK_DEFINITION=$(aws ecs describe-task-definition --task-definition vote-fargate --region us-east-1) 
                NEW_TASK_DEFINTIION=$(echo $TASK_DEFINITION | jq --arg IMAGE "$ECR_IMAGE" '.taskDefinition | .containerDefinitions[0].image = $IMAGE | del(.taskDefinitionArn) | del(.revision) | del(.status) | del(.requiresAttributes) | del(.compatibilities) | del(.registeredAt) | del(.registeredBy)') 
                NEW_TASK_INFO=$(aws ecs register-task-definition --region us-east-1 --cli-input-json "$NEW_TASK_DEFINTIION") 
                NEW_REVISION=$(echo $NEW_TASK_INFO | jq '.taskDefinition.revision') 
                aws ecs update-service --cluster Jenkins-worker --service vote --region us-east-1 --task-definition vote-fargate:${NEW_REVISION}           }
            
        }
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
