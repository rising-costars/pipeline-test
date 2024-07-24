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
        stage('Create Deploy') {
            steps {
              script {
                    postDeploys(
                        AutomationName: "VSIDeploys", 
                        DeployId: "${env.BUILD_ID}", 
                        BuildResult: "${currentBuild.currentResult}", 
                        BuildComponent: "connectall", 
                        ConnectALL_Api_Key: "${CONNECTALL_API_KEY}",
                        ConnectALL_Api_Url: "${CONNECTALL_API_URL}"
                    )
              }
            }
        }
        stage('Build Current Repo') {
            steps {
              script {
                    postCommits(
                        AutomationName: "VSICommits", 
                        DeployId: "${env.BUILD_ID}", 
                        GitRepoLoc: "./", 
                        PrevSuccessBuildCommit: "HEAD^1", 
                        CurrentBuildCommit: "HEAD",
                        ConnectALL_Api_Key: "${CONNECTALL_API_KEY}",
                        ConnectALL_Api_Url: "${CONNECTALL_API_URL}"
                        
                    )
              }
            }
        }
        stage('Build Second Repo') {
            steps {
              script {
                    sh 'rm -rf connectall-jenkins-shared-library'
                    sh 'git clone https://github.com/rising-costars/connectall-jenkins-shared-library.git'
                    postCommits(
                        AutomationName: "VSICommits", 
                        DeployId: "${env.BUILD_ID}", 
                        GitRepoLoc: "./connectall-jenkins-shared-library", 
                        PrevSuccessBuildCommit: "HEAD^1", 
                        CurrentBuildCommit: "HEAD",
                        ConnectALL_Api_Key: "${CONNECTALL_API_KEY}",
                        ConnectALL_Api_Url: "${CONNECTALL_API_URL}"
                        
                    )
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
                
                postDeploys(
                    AutomationName: "VSIDeploys", 
                    DeployId: "${env.BUILD_ID}", 
                    BuildResult: "${currentBuild.currentResult}", 
                    BuildComponent: "connectall", 
                    BuildStartTime: "${String.valueOf(currentBuild.timeInMillis / 1000))}",
                    BuildFinishTime: "${String.valueOf((currentBuild.timeInMillis + currentBuild.duration) / 1000))}",
                    ConnectALL_Api_Key: "${CONNECTALL_API_KEY}",
                    ConnectALL_Api_Url: "${CONNECTALL_API_URL}"
                    
                )
            
            }
        }
    }
}