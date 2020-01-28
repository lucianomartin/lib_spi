@Library('xmos_jenkins_shared_library@develop') _

getApproval()

pipeline {
  agent {
    label 'x86_64&&brew'
  }
  environment {
    REPO = 'lib_spi'
    VIEW = "${env.JOB_NAME.contains('PR-') ? REPO+'_'+env.CHANGE_TARGET : REPO+'_'+env.BRANCH_NAME}"
  }
  options {
    skipDefaultCheckout()
  }
  stages {
    stage('Get View') {
      steps {
        xcorePrepareSandbox("${VIEW}", "${REPO}")
      }
    }
    stage('Library Checks') {
      steps {
        xcoreLibraryChecks("${REPO}")
      }
    }
    stage('Legacy Tests') {
      steps {
        dir("${REPO}/legacy_tests") {
          viewEnv() {
            // Use Pipfile in legacy_tests, not lib_spi/Pipfile
            installPipfile(true)
            runPython("./runtests.py --junit-output=${REPO}_tests.xml")
          }
        }
      }
    }
    stage('Build') {
      steps {
        dir("${REPO}") {
          xcoreAllAppNotesBuild('examples')
          dir("${REPO}") {
            //runXdoc('doc')
          }
        }
      }
    }
  }
  post {
    success {
      updateViewfiles()
    }
    cleanup {
      xcoreCleanSandbox()
    }
  }
}
