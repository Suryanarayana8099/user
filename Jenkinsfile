def label = 'shopagent'
podTemplate(label: label, yaml: '''
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: build
  annotations:
    sidecar.istio.io/inject: "false"
spec:
  containers:
  - name: build
    image: kumararj/eos-jenkins-agent-base:latest
    command:
    - cat
    tty: true
  - name: docker
    image: docker:latest
    securityContext:
      privileged: true
'''
) {
    node(label) {
        stage('CleanWorkspace') {
          cleanWs()
        }
        stage('Checkout SCM') {
          git credentialsId: 'git', url: 'https://github.com/Kumar-arj/microservice-demo-user.git', branch: 'master'
        }

    stage('Docker Build') {
          container('docker') {
        stage('Build Image') {
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            def customImage = docker.build("kumararj/sock-shop-micro-services-user:${BUILD_NUMBER}")
            customImage.push()
            def finalImage = docker.build("kumararj/sock-shop-micro-services-user:latest")
            finalImage.push()
          }
        }
          }
    }
    stage('Docker Build DB') {
       dir('docker/user-db') {

          container('docker') {
        stage('Build DB Image') {
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            def customImage = docker.build("kumararj/sock-shop-micro-services-user-db:${BUILD_NUMBER}")
            customImage.push()
            def finalImage = docker.build("kumararj/sock-shop-micro-services-user-db:latest")
            finalImage.push()
          }
        }
          }
       }
    }

        stage('Helm Chart') {
          container('build') {
            dir('charts') {
              withCredentials([usernamePassword(credentialsId: 'nexuslogin', usernameVariable: 'username', passwordVariable: 'password')]) {
              sh '/usr/local/bin/helm package micro-services-user'
              sh 'curl -v -u $username:$password --upload-file micro-services-user-1.0.tgz http://nexus.k4m.in/repository/sock-shop-helm-local/'
              }
            }
          }
        }
    }
}

