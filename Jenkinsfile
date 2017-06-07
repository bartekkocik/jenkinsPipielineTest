
properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', artifactNumToKeepStr: '1', numToKeepStr: '1']]])

if (env.BRANCH_NAME == 'master') {

    stage('promote-staging')

    input "Promote to staging test?"


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

    input "Promote to production test repo?"


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

