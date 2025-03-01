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
            sh "scp -o StrictHostKeyChecking=no *.yaml ubuntu@${ANSIBLE_SERVER}:/home/ubuntu"
            sh "scp -r roles/* ubuntu@${ANSIBLE_SERVER}:/home/ubuntu/roles"
            sh "scp project-var ubuntu@${ANSIBLE_SERVER}:/home/ubuntu"
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
            sshCommand remote: remote, command: "ansible-playbook deploy-docker-with-roles.yaml"
          }
        }
      }
    }      
  }
} 
