def jsonData // Declare jsonData at the pipeline level

pipeline {
    agent any

    stages {
        stage('ReadJSON') {
            steps {
                script {
                    def jsonFile = readFile 'Test.json'
                    jsonData = new groovy.json.JsonSlurper().parseText(jsonFile)
                }
            }
        }
        stage('ProcessData') {
            steps {
                script {
                    // Check if jsonData is not null before processing
                    if (jsonData) {
                        jsonData.each { item ->
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
