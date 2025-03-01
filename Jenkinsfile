pipeline {   
  agent any
  environment {
    ANSIBLE_SERVER = "18.196.43.76"
  }
  stages {
    stage("copy files to ansible server") {
      steps {
        script {
          echo "copying all neccessary files to ansible control node"
          sshagent(['ansible-server']) {
            sh "scp -o StrictHostKeyChecking=no ansible/* ubuntu@${ANSIBLE_SERVER}:/home/ubuntu"

          }
        }
      }
    }
    stage ("execute ansible playbook") {
      steps {
        script {
          echo "calling ansible playbook to configure ec2 instances"
          def remote = [:]
          remote.name = "ansible-server"
          remote.host = ANSIBLE_SERVER
          remote.allowAnyHosts = true

          withCredentials([sshUserPrivateKey(credentialsId: 'ansible-server', keyFileVariable: 'keyfile', usernameVariable: 'user')]) {
            remote.user = user
            remote.identityFile = keyfile
            sshScript remote: remote, script: "prepare-ansible-server.sh"
            sshCommand remote: remote, command: "ansible-playbook my-playbook.yaml"
          }
        }
      }
    }      
  }
} 
