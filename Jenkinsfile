pipeline {
  agent any

  environment {
    NGINX_USER = "ubuntu"         // Ubuntu AMI default user
    NGINX_HOST = "172.31.94.139"  // Replace with your NGINX EC2 public IP
    SSH_CRED_ID = "nginx-ssh-key" // Jenkins SSH credential ID
    WEBSITE_DIR = "/var/www/html" // NGINX web root
  }

  stages {
    stage('Clone HTML Website') {
      steps {
        git branch: 'main', url: 'https://github.com/tirucloud/handtime.git'
      }
    }

    stage('Copy Website to NGINX') {
      steps {
        sshagent(credentials: [env.SSH_CRED_ID]) {
          sh """
            ssh -o StrictHostKeyChecking=no ${NGINX_USER}@${NGINX_HOST} 'sudo rm -rf ${WEBSITE_DIR}/*'
            scp -o StrictHostKeyChecking=no -r * ${NGINX_USER}@${NGINX_HOST}:/tmp/html_site
            ssh ${NGINX_USER}@${NGINX_HOST} 'sudo cp -r /tmp/html_site/* ${WEBSITE_DIR}/ && sudo systemctl restart nginx'
          """
        }
      }
    }
  }

  post {
    success {
      echo "Website deployed successfully to NGINX!"
    }
    failure {
      echo "Deployment failed. Please check the logs."
    }
  }
}
