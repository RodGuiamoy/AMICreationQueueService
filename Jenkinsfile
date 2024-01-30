import groovy.json.JsonSlurper

def readJsonFromFile(String filePath) {
    def slurper = new JsonSlurper()
    def jsonFile = new File(filePath)

    if (jsonFile.exists()) {
        def jsonContents = slurper.parseText(jsonFile.text)
        return jsonContents
    } else {
        return null
    }
}

def readFile(String filePath) {
    return new File(filePath).text
}

pipeline {
    agent any

    stages {
        stage('TEST') {
            steps {
                script {
                    // def fileContent = readFile('C:\\code\\AMICreationQueueService\\Test.txt')
                    // println fileContent

                    def filePath = 'C:\\code\\AMICreationQueueService\\Test.json'
                    def jsonData = readJsonFromFile(filePath)

                    if (jsonData) {
                        // Access JSON data here
                        def name = jsonData.Name
                        def age = jsonData.Age
                        println "Name: $name, Age: $age"
                        } 
                    else {
                        println "JSON file not found at $filePath"
                    }
                }
            }
        }
    }
}
