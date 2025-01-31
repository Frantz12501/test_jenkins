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
        sh '''
        docker build -t python-sum . --progress=plain || exit 1
        '''
    }
}


        
        stage('Run') {
            steps {
                echo 'Running Docker container...'
                script {
                    def output = bat(script: 'docker run -dit python-sum', returnStdout: true)
                    def lines = output.split('\n')
                    env.CONTAINER_ID = lines[-1].trim()
                    echo "Container ID: ${env.CONTAINER_ID}"
                }
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests inside the container...'
                script {
                    def testLines = readFile(TEST_FILE_PATH).split('\n')
                    for (line in testLines) {
                        def vars = line.split(' ')
                        def arg1 = vars[0]
                        def arg2 = vars[1]
                        def expectedSum = vars[2].toFloat()
                        
                        // Exécuter sum.py dans le conteneur avec les arguments
                        def output = bat(script: "docker exec ${env.CONTAINER_ID} python /app/sum.py ${arg1} ${arg2}", returnStdout: true)
                        def result = output.split('\n')[-1].trim().toFloat()
                        
                        // Vérifier si le résultat correspond à la valeur attendue
                        if (result == expectedSum) {
                            echo "Test passed for inputs ${arg1}, ${arg2}: Expected and got ${result}"
                        } else {
                            error "Test failed for inputs ${arg1}, ${arg2}: Expected ${expectedSum}, but got ${result}"
                        }
                    }
                }
            }
        }
        
    }
    
    post {
        always {
            echo 'Stopping and removing the container...'
            bat '''
                docker stop %CONTAINER_ID%
                docker rm %CONTAINER_ID%
            '''
        }
    }
}