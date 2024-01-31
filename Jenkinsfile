
pipeline {
    agent any

    stages {
        stage('ReadJSON') {
            steps {
                script {
                    // Assuming the JSON file is named 'data.json' and located in the workspace
                    def jsonFile = readFile 'Test.json'
                    def jsonData = new groovy.json.JsonSlurper().parseText(jsonFile)

                    // Loop through each item in the JSON array
                    jsonData.each { item ->
                        echo "Processing: ${item.environment}"
                        // Add your processing logic here
                        // For example:
                        echo "Environment: ${item.environment}"
                        echo "Instance Names: ${item.instanceNames}"
                        echo "Ticket Number: ${item.ticketNumber}"
                        echo "Mode: ${item.mode}"
                        echo "Date: ${item.date}"
                        echo "Time: ${item.time}"
                        echo "Scheduled Build ID: ${item.scheduledBuildId}"
                    }
                    
                }
            }
        }
        // stage('GetUpcomingBuilds') {
        //     steps {
        //         script {

                    
        //         }
        //     }
        // }
    }
}
