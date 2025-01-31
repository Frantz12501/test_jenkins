pipeline {
    agent any

    environment {
        CONTAINER_ID_RUN = ''
        SUM_PY_PATH = './sum.py'
        DIR_PATH = '.'
    }

    stages {
        stage('Check Docker Version and Build Image') {
            steps {
                echo 'Building the Docker image and checking Docker version....'
                bat '''
                call build_and_run.bat
                '''
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running tests inside the container...'
                bat '''
                docker cp "%SUM_PY_PATH%" "python-sum-container:/app/sum.py"
                for /F "tokens=1,2,3 delims= " %%A in (test_variables.txt) do (
                    set NUM1=%%A
                    set NUM2=%%B
                    set EXPECTED=%%C
                    for /F %%R in ('docker exec python-sum-container python /app/sum.py !NUM1! !NUM2!') do (
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
                docker stop python-sum-container
                docker rm python-sum-container
                '''
            }
        }
    }
}
