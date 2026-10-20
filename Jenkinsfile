
pipeline {
    agent any

    stages {
        stage('Code Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Mukesh-ph-git/jenkins-github-demo.git'

                echo 'Code checkout successful'
            }
        }

        stage('Validation') {
            steps {
                sh '''
                    set -eu
                    test -s index.html
                    grep -qi '<html' index.html
                    echo "HTML validation successful"
                '''
            }
        }

        stage('Deployment') {
            steps {
                sshagent(credentials: ['ec2-ssh-key']) {
                    sh '''
                        set -eu

                        echo "Deploying website to EC2"

                        ssh -o StrictHostKeyChecking=yes \
                            ubuntu@54.91.84.56 \
                            'test -d /var/www/html'

                        rsync -avz \
                            -e "ssh -o StrictHostKeyChecking=yes" \
                            index.html \
                            ubuntu@54.91.84.56:/tmp/index.html

                        ssh -o StrictHostKeyChecking=yes \
                            ubuntu@54.91.84.56 \
                            'sudo install -m 644 /tmp/index.html /var/www/html/index.html'

                        echo "Verifying deployed website"

                        curl -fsS http://54.91.84.56/ -o deployed.html

                        grep -qi '<html' deployed.html

                        echo "Deployment successful!"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'All stages completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check Console Output.'
        }
    }
}
