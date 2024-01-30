def readFile(String filePath) {
    return new File(filePath).text
}

pipeline {
    agent any

    stages {
        stage('TEST') {
            steps {
                script {
                    def fileContent = readFile('C:\\code\\AMICreationQueueService\\Test.txt')
                    println fileContent
                }
            }
        }
    }
}
