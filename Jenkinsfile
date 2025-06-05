pipeline {
    agent { label 'agent-php' }
    stages {
        stage('Git Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/KONATE-ABDRAHAMANE/TP_backend_deployment_cda.git'
            }
        }

        stage('Déploiement via FTP') {
            steps {
                sh '''
                    lftp -d -u ${siteUser},${sitePass} ftp://ftp-lafia-market-back.alwaysdata.net -e "
                        mirror -R . www/;
                        bye
                    "
                '''
            }
        }

        stage('Install Composer & .env') {
            steps {
                sh """
                    sshpass -p ${sitePass} ssh -o StrictHostKeyChecking=no lafia-market-back@ssh-lafia-market-back.alwaysdata.net '
                        cd ~/www &&
                        composer install --no-dev &&
                        echo "DB_HOST=mysql-lafia-market-back.alwaysdata.net" > .env &&
                        echo "DB_NAME=${dbName}" >> .env &&
                        echo "DB_USER=${dbUser}" >> .env &&
                        echo "DB_PASS=${dbPass}" >> .env
                    '
                """
            }
        }

        stage('Migration bdd') {
            steps {
                sh """
                    sshpass -p ${sitePass} ssh -o StrictHostKeyChecking=no lafia-market-back@ssh-lafia-market-back.alwaysdata.net '
                        cd ~/www && php migrate.php
                    '
                """
            }
        }
    }
}
