pipeline {
    agent any

    environment {
        CONTAINER_ID_RUN = ''
        SUM_PY_PATH = './sum.py'
        DIR_PATH = '.'
    }

    stages {
        stage('Check Docker Version') {
            steps {
                echo 'Checking Docker version...'
                bat '''
                docker --version
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building the Docker image ....'
                echo "${DIR_PATH}"
                bat '''
                docker build -t python-sum . || exit /b 1
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                echo 'Running the Docker container...'
                script {
                    env.CONTAINER_ID_RUN = bat(script: 'docker run -dit python-sum', returnStdout: true).trim()
                    echo "Container ID: ${env.CONTAINER_ID_RUN}"
                }
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running tests inside the container...'
                bat '''
                docker cp "%SUM_PY_PATH%" "%CONTAINER_ID_RUN%:/app/sum.py"
                for /F "tokens=1,2,3 delims= " %%A in (test_variables.txt) do (
                    set NUM1=%%A
                    set NUM2=%%B
                    set EXPECTED=%%C
                    for /F %%R in ('docker exec %CONTAINER_ID_RUN% python /app/sum.py !NUM1! !NUM2!') do (
                        set RESULT=%%R
                    )
                    if !RESULT! NEQ !EXPECTED! (
                        echo Test failed for inputs !NUM1!, !NUM2!: Expected !EXPECTED!, but got !RESULT!
                        exit /b 1
                    ) else (
                        echo Test passed for inputs !NUM1!, !NUM2!
                    )
                )
                '''
            }
        }

        stage('Cleanup') {
            steps {
                echo 'Stopping and removing the container...'
                bat '''
                docker stop ${CONTAINER_ID_RUN}
                docker rm ${CONTAINER_ID_RUN}
                '''
            }
        }
    }
}
