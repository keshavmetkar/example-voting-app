pipeline{
   //agent any
   agent {label 'worker'}
   options{
    buildDiscarder(logRotator(numToKeepStr: '15'))
    disableConcurrentBuilds()
    retry(2)
    timeout(time: 20, unit: 'MINUTES')
   }
   parameters{
    string(name: 'BRANCH', defaultValue: 'master')
    choice(name: 'ENVIRONMENT', choices: ['Dev','QA', 'Prod'])
    booleanParam(name: 'TEST_CASES', defaultValue: false)
   }
   triggers{
    cron('H */4 * * *')
    pollSCM('H */4 * * *')
   }
   stages{
    stage('Build and Push'){
        steps{
            sh '''
            echo STARTING THE EXECUTION
            cd vote
            docker build -t 724409163191.dkr.ecr.us-east-1.amazonaws.com/voting-app:v${BUILD_NUMBER} .
            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 724409163191.dkr.ecr.us-east-1.amazonaws.com
            docker push 724409163191.dkr.ecr.us-east-1.amazonaws.com/voting-app:v${BUILD_NUMBER}
            '''
        }
    }
    stage('Deploy Stage'){
        steps{
            sh '''
            TASK_DEFINITION=$(aws ecs describe-task-definition --task-definition voting-app-td --region us-east-1)
            ECR_IMAGE="724409163191.dkr.ecr.us-east-1.amazonaws.com/voting-app:v${BUILD_NUMBER}"
            NEW_TASK_DEFINTIION=$(echo $TASK_DEFINITION | jq --arg IMAGE "$ECR_IMAGE" '.taskDefinition | .containerDefinitions[0].image = $IMAGE | del(.taskDefinitionArn) | del(.revision) | del(.status) | del(.requiresAttributes) | del(.compatibilities) | del(.registeredAt) | del(.registeredBy)') 
            NEW_TASK_INFO=$(aws ecs register-task-definition --region us-east-1 --cli-input-json "$NEW_TASK_DEFINTIION")
            NEW_REVISION=$(echo $NEW_TASK_INFO | jq '.taskDefinition.revision') 
            aws ecs update-service --cluster voting-app --service vote --region us-east-1 --task-definition voting-app-td:${NEW_REVISION}
            aws ecs wait services-stable --cluster voting-app --service vote
            '''
        }
    }
   }
   post{
    always{
        echo "Always Run"
    }
   }
}
