@Library('connectall_library') _
pipeline {
    agent any
    options {
        timestamps(); parallelsAlwaysFailFast(); timeout(time: 6, unit: 'HOURS')
    }
    environment {
        REPOS_TO_BE_BUILT = "${reposToBeBuilt}"
        CONNECTALL_API_KEY = credentials('connectall-api-key')
        CONNECTALL_API_URL = credentials('connectall-api-url')
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
        stage('Build Current Repo') {
            steps {
              script {
                    postCommits(CONNECTALL_API_URL: "${CONNECTALL_API_KEY}", CONNECTALL_API_KEY: "${CONNECTALL_API_KEY}", AutomationName: "VSIChanges", DeployId: "${env.BUILD_ID}", GitRepoLoc: "./", PrevSuccessBuildCommit: "HEAD^1", CurrentBuildCommit: "HEAD" )
              }
            }
        }
        stage('Build Second Repo') {
            steps {
              script {
                    sh 'git clone https://github.com/rising-costars/connectall-jenkins-shared-library.git'
                    postCommits(CONNECTALL_API_URL: "${CONNECTALL_API_KEY}", CONNECTALL_API_KEY: "${CONNECTALL_API_KEY}", AutomationName: "VSIChanges", DeployId: "${env.BUILD_ID}", GitRepoLoc: "./connectall-jenkins-shared-library", PrevSuccessBuildCommit: "HEAD~1", CurrentBuildCommit: "HEAD" )
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