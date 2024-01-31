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

def jsonData = []

pipeline {
    agent any

    // build schedule

    stages {
        stage('ReadJSON') {
            steps {
                script {

                    def filePath = 'C:\\code\\AMICreationQueueService\\Test.json'
                    jsonData = readJsonFromFile(filePath)

                    if (!jsonData) {

                        // println "JSON file not found at $filePath"
                        error ("Null JSON content.")
                       
                    } 

                    // // Access JSON data here
                    // def name = jsonData.Name
                    // def age = jsonData.Age
                    // println "Name: $name, Age: $age"
                    
                }
            }
        }
        stage('GetUpcomingBuilds') {
            steps {
                script {

                    jsonData.each { scheduledBuild -> 
                        def environment = scheduledBuild.Name
                        def instanceNames = scheduledBuild.Age
                        
                        println "Environment: $environment, InstanceNames: $instanceNames"
                    }
                    // Get builds that are already past due, and set to be run in the next 15 minutes from Json content
                    // Calculate delay and add it as a property
                }
            }
        }
        // stage('ScheduleBuilds') {
        //     steps {
        //         script {
        //             // Loop through upcoming builds and schedule them
        //         }
        //     }

        // }
    }
}
