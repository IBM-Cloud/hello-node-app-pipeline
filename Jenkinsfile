def gitPath="https://github.com/IBM-Cloud/hello-node-app"
pipeline {
  agent any
  options {
    timeout(time: 20, unit: 'MINUTES') 
  }
  stages {
    stage('create projects') {
      steps {
        script {
          openshift.withCluster() {
            //TODO: sh './010'
            //sh 'oc new-project development'
            //sh 'oc new-project testing'
            //sh 'oc new-project production'
            sh './010-create-projects.sh'
            openshift.withProject() {
              echo "Using project: ${openshift.project()}"
            }
          }
        }
      }
    }
    stage('build') {
      steps {
        script {
          openshift.withCluster() {
            openshift.withProject('development') {
              //sh 'oc policy add-role-to-group system:image-puller system:serviceaccounts:production'
              //sh 'oc policy add-role-to-group system:image-puller system:serviceaccounts:testing'
              //sh 'oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins'
             // openshift.newApp(gitPath).narrow('svc').expose()
              // NOTE: the selector returned when -F/--follow is supplied to startBuild()
              // will be inoperative for the various selector operations.
              // Consider removing those options from startBuild and using the logs()
              // command to follow the build output.
              // openshift.selector('bc', 'hello-node-app').startBuild('--follow', '--wait')
              sh './020-development.sh'
            }
          }
        }
      }
    }

    stage('approval (testing)') {
      steps {
        script {
          input message: 'Approve for testing?', id: 'approval'
        }
      }
    }

    stage('deploy to testing') {
      steps {
        script {
          openshift.withCluster() {
            openshift.withProject('testing') {
              //sh 'oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins'
              //openshift.tag('development/hello-node-app:latest', 'hello-node-app:test')
              //openshift.newApp("--image-stream=hello-node-app:test").narrow('svc').expose()
              sh './030-testing.sh'
            }
          }
        }
      }
    }

    stage('approval (production)') {
      steps {
        script {
          input message: 'Approve for production?', id: 'approval'
        }
      }
    }

    stage('deploy to production') {
      steps {
        script {
          openshift.withCluster() {
            openshift.withProject('production') {
              //sh 'oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins'
              //openshift.tag('testing/hello-node-app:test', 'hello-node-app:prod')
             // openshift.newApp("--image-stream=hello-node-app:prod").narrow('svc').expose()
             sh './040-production.sh'
            }
          }
        }
      }
    }
  }
}
