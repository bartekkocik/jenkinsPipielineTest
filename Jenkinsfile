
properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', artifactNumToKeepStr: '1', numToKeepStr: '1']]])

if (env.BRANCH_NAME == 'master') {

 echo "promoting to staging"

stage('rollout-staging')
node {
	try {
            sh "git checkout staging"
            sh "git merge -s -Xtheirs master"
            sh "git push origin HEAD"
	} catch (e) {
            throw e
    }
}


    echo "Promote to production test repo"
	
stage('rollout-production')
node {
        try {
            sh "git checkout production"
            sh "git merge -s -Xtheirs staging"
            sh "git push origin HEAD"
        } catch (e) {
            throw e
        }
}
}

