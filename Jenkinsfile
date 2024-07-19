pipeline {
    
    options {
        timestamps(); parallelsAlwaysFailFast(); ansiColor('xterm'); timeout(time: 6, unit: 'HOURS')
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
                manager.addShortText('BUILD: SUCCESS', 'black', 'lightgreen', '0.5px', 'black')
            }
        }
        unstable {
            script {
                echo 'Build unstable'
                manager.addShortText('BUILD: UNSTABLE', 'black', 'orange', '0.5px', 'black')
            }
        }
        aborted {
            script {
                echo 'Build aborted'
                manager.addShortText('BUILD: ABORTED', 'black', 'grey', '0.5px', 'black')
            }
        }
        failure {
            script {
                echo 'Build failed'
                manager.addShortText('BUILD: FAILED', 'black', 'red', '0.5px', 'black')
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