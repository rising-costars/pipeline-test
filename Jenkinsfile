@Library('connectall_library') _
pipeline {
    agent any
    options {
        timestamps(); parallelsAlwaysFailFast(); timeout(time: 6, unit: 'HOURS')
    }
    environment {
        REPOS_TO_BE_BUILT = "${reposToBeBuilt}"
    }
    parameters {
         booleanParam(name: 'BLACKDUCK_SCAN', defaultValue: false, description: 'Enable blackduck scan')
         string(name:'BLD_AGENT_LABEL', defaultValue: 'test-agent', description: 'Label of the build agent')
    }

    stages {
        stage('Prepare') {
            steps {
              script {
                    String javaHome = "${env.javaHome}"
                    sh '''
                    ls -lrt
                    git status
                    echo "test again"
                    '''
              }
            }
        }
        stage('Build') {
            steps {
              script {
                    helloWorld(name: "Akshitha", day: "Taco Tuesday")
                    postCommits(CONNECTALL_API_URL: "https://connectall183.clarityrox.com/ua", CONNECTALL_API_KEY: "asdfasdfas", automationName: "VSIChanges", deployId: "abc123" )
              }
            }
        }
    }

    post {
        success {
            script {
                echo 'Build success'
            }
        }
        unstable {
            script {
                echo 'Build unstable'
            }
        }
        aborted {
            script {
                echo 'Build aborted'
            }
        }
        failure {
            script {
                echo 'Build failed'
            }
        }
        always {
            script {
                helloWorld(day: "Taco Tuesday", name: "Sharath")
                echo 'Clean up of node modules'
                sh '''
                    ls -lrt
                    git status
                '''
            }
        }
    }
}