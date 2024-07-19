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
                    echo 'Git commit : ${gitVars.GIT_COMMIT}'
                    env
                    '''
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
                echo 'Clean up of node modules'
                sh '''
                    ls -lrt
                    git status
                '''
            }
        }
    }
}