
properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', artifactNumToKeepStr: '5', numToKeepStr: '5']]])

if (env.BRANCH_NAME == 'master') {

    stage('promote-staging')

    input "Promote to staging?"


    stage('rollout-staging')
    node {
        try {
            sh "git checkout staging"
            sh "git merge -s theirs master"
            sh "git push origin HEAD"
        } catch (e) {
            throw e
        }
    }

    stage('promote-production')

    input "Promote to production?"


    stage('rollout-production')
    node {
        try {
            sh "git checkout production"
            sh "git merge -s theirs staging"
            sh "git push origin HEAD"
        } catch (e) {
            throw e
        }
    }
}

