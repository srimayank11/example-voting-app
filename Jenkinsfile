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
                sh "echo Helloooo"
                sh "sleep 10"
            }
        }
        stage("Second Stage"){
            steps{
                sh "echo Helloooo"
            }
            
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
