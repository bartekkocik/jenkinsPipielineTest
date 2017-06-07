
properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', artifactNumToKeepStr: '1', numToKeepStr: '1']]])

stage 'Checkout' {

	checkout scm
}

if (env.BRANCH_NAME == 'master') {

    input "Promote to staging test?"


        try {
            sh "git checkout staging"
            sh "git merge -s theirs master"
            sh "git push origin HEAD"
        } catch (e) {
            throw e
        }


    input "Promote to production test repo?"


        try {
            sh "git checkout production"
            sh "git merge -s theirs staging"
            sh "git push origin HEAD"
        } catch (e) {
            throw e
        }
}

