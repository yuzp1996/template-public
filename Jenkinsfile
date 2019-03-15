@Library('alauda-cicd') _

def GIT_COMMIT
def GIT_BRANCH

pipeline{
  agent {
      label "golang"
  }

  environment {
    NAME = "pipeline-templates"
    DINGDING_BOT = "ding-jenkins-webhook"
  }

  stages{
    stage("InstallJenkinsfileXT"){
        steps{
            git branch:"feature/add-lint-tool", url:"https://bitbucket.org/mathildetech/jenkinsfilext", credentialsId: "chengjingtao-bitbucket"
            sh 'mkdir -p ${GOPATH}/src/bitbucket.org/mathildetech/jenkinsfilext'
            sh 'rm -r ${GOPATH}/src/bitbucket.org/mathildetech/jenkinsfilext || true'
            sh 'ls -la && cp -r ./ ${GOPATH}/src/bitbucket.org/mathildetech/jenkinsfilext/'
            sh 'go install bitbucket.org/mathildetech/jenkinsfilext'
        }
    }
    stage("Lint"){
      steps{
        script {
          def scmVar = checkout scm
          GIT_COMMIT = scmVar.GIT_COMMIT
          GIT_BRANCH = scmVar.GIT_BRANCH
          sh "jenkinsfilext validate definition -d ./"    
        }
      }
    }
  }

  post {
      // 成功
      success {
        script {
            deploy.notificationSuccess("${NAME}", DINGDING_BOT, "流水线完成了", "${GIT_BRANCH} ${GIT_COMMIT}")
        }
      }
      // 失败
      failure {
          script{
            deploy.notificationFailed("${NAME}", DINGDING_BOT, "流水线失败了", "${GIT_BRANCH} ${GIT_COMMIT}")
          }
      }
      // 取消的
      aborted {
        echo "aborted!"
      }
  }
}
