pipeline {
    agent any
    
    environment {
        SUM_PY_PATH = './sum.py'
        DIR_PATH = '.'
        TEST_FILE_PATH = './test_variables.txt'
        
    }
    
    stages {
        stage('Build Docker Image') {
    steps {
        echo 'Building the Docker image ....'
        echo "${DIR_PATH}"
        powershell '''
        docker build -t python-sum . || exit 1
        '''
    }
}

stage('Cleanup') {
    steps {
        echo 'Stopping and removing the container...'
        powershell '''
        docker stop ${CONTAINER_ID_RUN}
        docker rm ${CONTAINER_ID_RUN}
        '''
    }
}


        
        
        
        }
        
    
    
    
}