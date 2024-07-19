freeStyleJob('sb704005/vsi-build-post') {
    description 'Send metrics to connectall'
    logRotator(-1, 100)
    label 'k8s-utility-node'
    concurrentBuild(true)

    wrappers {
        timestamps()
        colorizeOutput 'xterm'
    }

    parameters {
      stringParam('CONNECTALL_API_URL',  'https://connectall183.clarityrox.com/ua', 'e.g. http://localhost:8090')
      stringParam('CONNECTALL_API_KEY', 'aaaa-bbbb-cccc-dddd-eeeeeeeeeee', 'Api Key for connectall account')

      // According to https://github.com/jenkinsci/jenkins/blob/master/core/src/main/java/hudson/model/Result.java
      stringParam('BUILD_RESULT', '', 'The result of the build. (e.g. SUCCESS, UNSTABLE, FAILURE, NOT_BUILT, ABORTED, etc.)')
      stringParam('MAIN_COMMIT_SHA', '', 'The main commit of the deployed change.')
      stringParam('TIME_CREATED_MS', '', """The time the deployed change first began.
      In this case, the earlist timestamp in the set of commits. Usually the calling job's (currentBuild.timeInMillis / 1000).
      """)
      stringParam('TIME_DEPLOYED_MS', '', 'The time the deploy job finished. Usually the calling job\'s (currentBuild.timeInMillis + currentBuild.duration) / 1000.')
    }

    steps {
      shell '''\
      #!/bin/bash
      echo $(date -u -d @$TIME_CREATED_MS "+%Y-%m-%dT%H:%M:%S")
      echo $(date -u -d @$TIME_DEPLOYED_MS "+%Y-%m-%dT%H:%M:%S")

      json=$(cat <<EOF
      {\"appLinkName\":\"some-unique-connectall-applink-name-such-as-TellerDeploys2\",
          \"fields\": {
            \"Id\": \"$BUILD_NUMBER\",
            \"IsSuccessful\":$([[ "$BUILD_RESULT" == "SUCCESS" ]]  IS_SUCCESSFUL=true || && IS_SUCCESSFUL=false; echo $IS_SUCCESSFUL),
            \"TimeCreated\":\"$(date -u -d @$TIME_CREATED_MS "+%Y-%m-%dT%H:%M:%S")\",
            \"TimeDeployed\":\"$(date -u -d @$TIME_DEPLOYED_MS "+%Y-%m-%dT%H:%M:%S")\",
            \"MainRevision\":\"$MAIN_COMMIT_SHA\"
          }
      }
      EOF
      )

      echo "Send JSON: $json"
      echo "via ${CONNECTALL_API_URL}/connectall/api/2/postRecord?apikey=$CONNECTALL_API_KEY"

      curl --header "Content-Type: application/json;charset=UTF-8" -X POST -d "$json" "${CONNECTALL_API_URL}/connectall/api/2/postRecord?apikey=$CONNECTALL_API_KEY"
      '''.stripIndent()
    }
}
 