pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'paycare-etl'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/Djohell13/paycare.git', branch: 'main'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'pytest --junitxml=unit-tests.xml'
            }
            post {
                always {
                    junit 'unit-tests.xml'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    sh '''
                        echo "employee_id,employee_name,salary" > input_data.csv
                        echo "101,Alice,5000" >> input_data.csv
                        echo "102,Bob,7000" >> input_data.csv
                    '''

                    sh """
                        docker run --rm \
                          -v \$(pwd)/input_data.csv:/app/input_data.csv \
                          -v \$(pwd)/output_data.csv:/app/output_data.csv \
                          ${DOCKER_IMAGE}
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ ETL Pipeline completed successfully!'
        }
        failure {
            echo '❌ ETL Pipeline failed.'
        }
    }
}
