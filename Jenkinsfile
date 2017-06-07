
properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', artifactNumToKeepStr: '1', numToKeepStr: '1']]])

if (env.BRANCH_NAME == 'master') {

 echo "promoting to staging"

	stage('rollout-staging') {
		node {
			try {
				sh "git checkout staging"
				sh "git merge -s recursive -X theirs origin/master"
				sh "git push"
			} catch (e) {
					echo "promotion error"
					throw e
			}
		}
	}

    echo "Promote to production test repo"
	
	stage('rollout-production') {
		node {
			try {
				sh "git checkout production"
				sh "git merge -s recursive -X theirs origin/staging"
				sh "git push"
			} catch (e) {
				echo "promotion error"
				throw e
			}
		}
	}
}



