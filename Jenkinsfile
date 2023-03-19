Pipeline{
    agent any
    options {
        buildDiscarder(logRotator(num_to_keep_str:'15'))
        disableConcurrentbuilds()
        retry(2)
        timeout(time: 1, unit: 'MINUTES')
    }
    parameters{
        string(name:'BRANCH' defaultValue:'master')
        choice(name:'ENVIRONMENT' choices:['dev','qa','prod']) 
        booleanParam(name:'TEST_CASES' defaultValue:'false') 
    }
        stages{
            stage(‘First stage’){
                     steps{
                           sh “echo Hello”
                     }
              }
             stage(‘Second Stage’){
                      steps{
                           sh “sleep 30”
                     }
               }
              }
        }
