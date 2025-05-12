pipeline {
    agent any

    environment {
        OPTION_A = "Cats"
        OPTION_B = "Dogs"
        PORT = "80"
        VENV_DIR = ".venv"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/MoizArcana/voteapp.git'
            }
        }

        stage('Set up Python Environment') {
            steps {
                sh '''
                    python3 -m venv ${VENV_DIR}
                    source ${VENV_DIR}/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Application') {
            steps {
                sh '''
                    # Kill any existing app on port 80 (optional, Unix only)
                    fuser -k ${PORT}/tcp || true

                    source ${VENV_DIR}/bin/activate
                    nohup gunicorn app:app -b 0.0.0.0:${PORT} \
                        --log-file - --access-logfile - --workers 4 --keep-alive 0 > app.log 2>&1 &
                '''
            }
        }
    }

    post {
        success {
            echo 'Flask app started successfully.'
        }
        failure {
            echo 'There was a problem running the app.'
        }
    }
}
