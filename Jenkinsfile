@Library('falcon-pipeline-support') _

properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', artifactNumToKeepStr: '5', numToKeepStr: '5']]])
slackNotify('STARTED')

stage('checkout')
try {
    node {
        checkout scm
    }
} catch (e) {
    slackNotify('FAILED on checkout')
    throw e
}

stage('validate')
try {
    node {
        sh 'yamllint -d "{extends: default, rules: {comments-indentation: disable, line-length: disable, new-line-at-end-of-file: { level: warning }, indentation: { indent-sequences: no}}}" .'
    }
} catch (e) {
    slackNotify('FAILED on validation')
    throw e
}

if (env.BRANCH_NAME == 'master') {

    stage('promote-staging')
    milestone()
    input "Promote to staging?"
    milestone()

    stage('rollout-staging')
    node {
        try {
            sh "git checkout staging"
            sh "git merge -s theirs master"
            sh "git push origin HEAD"
        } catch (e) {
            slackNotify('FAILED merging master to staging or pushing')
            throw e
        }
    }

    stage('promote-production')
    milestone()
    input "Promote to production?"
    milestone()

    stage('rollout-production')
    node {
        try {
            sh "git checkout production"
            sh "git merge -s theirs staging"
            sh "git push origin HEAD"
        } catch (e) {
            slackNotify('FAILED merging staging to production or pushing')
            throw e
        }
    }
}

slackNotify('SUCCESSFUL')
