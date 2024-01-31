def jsonData // Declare jsonData at the pipeline level

pipeline {
    agent any

    stages {
        stage('ReadJSON') {
            steps {
                script {
                    def jsonFile = readFile 'Test.json'
                    def parsedJson = new groovy.json.JsonSlurper().parseText(jsonFile)
                    // Convert LazyMap to a serializable HashMap
                    jsonData = parsedJson.collectEntries { k, v -> [k, v] }
                }
            }
        }
        stage('ProcessData') {
            steps {
                script {
                    if (jsonData) {
                        jsonData.each { key, value ->
                            def item = value // Assuming each item in jsonData is a map
                            echo "Processing: ${item.environment}"
                            // Add your processing logic here
                            echo "Environment: ${item.environment}"
                            echo "Instance Names: ${item.instanceNames}"
                            echo "Ticket Number: ${item.ticketNumber}"
                            echo "Mode: ${item.mode}"
                            echo "Date: ${item.date}"
                            echo "Time: ${item.time}"
                            echo "Scheduled Build ID: ${item.scheduledBuildId}"
                        }
                    } else {
                        echo "jsonData is null or not defined"
                    }
                }
            }
        }
        // Additional stages can be added here
    }
}
