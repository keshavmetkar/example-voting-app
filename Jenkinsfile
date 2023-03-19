pipeline{
   //agent any
   agent {label 'worker'}
   options{
    buildDiscarder(logRotator(numToKeepStr: '15'))
    disableConcurrentBuilds()
    retry(2)
    timeout(time: 1, unit: 'MINUTES')
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
            sh "sleep 10"
        }
    }
   }
   post{
    always{
        echo "Always Run"
    }
   }
}
