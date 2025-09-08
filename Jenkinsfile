pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-username/simple-python-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'python3 -m venv venv || true'
                sh '. venv/bin/activate && pip install --upgrade pip && pip install -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                sh '. venv/bin/activate && pytest'
            }
        }

        stage('Deploy') {
            steps {
                sshagent (credentials: ['deploy-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no deploy@YOUR_SERVER_IP 'bash -s' <<'EOF'
                      set -e
                      cd /home/deploy/app
                      git fetch origin
                      git checkout main
                      git pull origin main
                      if [ ! -d "venv" ]; then python3 -m venv venv; fi
                      . venv/bin/activate
                      pip install -r requirements.txt
                      sudo systemctl restart myapp
EOF
                    '''
                }
            }
        }
    }
}

