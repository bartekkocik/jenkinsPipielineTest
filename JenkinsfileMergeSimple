@Library('falcon-pipeline-support') _

properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', artifactNumToKeepStr: '5', numToKeepStr: '5']]])


stage('validate')
try {
    node {
        sh 'yamllint -d "{extends: default, rules: {comments-indentation: disable, line-length: disable, new-line-at-end-of-file: { level: warning }, indentation: { indent-sequences: no}}}" .'
    }
} catch (e) {
    slackNotify('FAILED on yaml validation')
    throw e
}

stage('log')
try {
    node {
        sh 'git log -1 > GIT_COMMIT'
        echo readFile('GIT_COMMIT')
    }
} catch (e) {
    slackNotify('FAILED log commit message')
    throw e
}



if (env.BRANCH_NAME == 'master') {

    stage('promote-staging')
    milestone()
    echo "Promote to staging"
    milestone()

    stage('rollout-staging')
    node {
            slackNotify('Unmerged changes. Rollout to staging')
            slackNotify(readFile('GIT_COMMIT'))

            sh "git checkout origin/staging"
            sh "git pull origin HEAD:staging"
            sh "git merge -s recursive -X theirs origin/master"
            sh "git push origin HEAD:staging"
    }
}

if (env.BRANCH_NAME == 'staging') {
    stage('promote-production')
    milestone()
    echo "Promote to production"
    milestone()

    stage('rollout-production')
    node {
            slackNotify('Unmerged changes. Rollout to production')
            slackNotify(readFile('GIT_COMMIT'))

            sh "git checkout origin/production"
            sh "git pull origin HEAD:production"
            sh "git merge -s recursive -X theirs origin/staging"
            sh "git push origin HEAD:production"
    }
}
