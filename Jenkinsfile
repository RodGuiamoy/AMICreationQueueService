class scheduledBuild {
    String environment
    String instanceNames
    String ticketNumber
    String mode
    String date
    String time
    String scheduledBuildId
}

def scheduledBuilds = []

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
                        // echo "Processing: ${item.environment}"
                        // // Add your processing logic here
                        // // For example:
                        // echo "Environment: ${item.environment}"
                        // echo "Instance Names: ${item.instanceNames}"
                        // echo "Ticket Number: ${item.ticketNumber}"
                        // echo "Mode: ${item.mode}"
                        // echo "Date: ${item.date}"
                        // echo "Time: ${item.time}"
                        // echo "Scheduled Build ID: ${item.scheduledBuildId}"

                        scheduledBuilds << new scheduledBuild(environment: item.environment,  instanceNames: item.instanceNames, ticketNumber: item.ticketNumber, mode: item.mode, date: item.date, time: item.time, scheduledBuildId: item.scheduledBuildId)
                    }
                    
                }
            }
        }
        stage('GetUpcomingBuilds') {
            steps {
                script {
                    def scheduledBuildsStr = "Scheduled builds:\n"
                    scheduledBuildsStr += "-----------------------\n"
                    scheduledBuilds.each { item ->
                        scheduledBuildsStr += "Environment: ${item.environment}\n"
                        scheduledBuildsStr += "Instance Names: ${item.instanceNames}\n"
                        scheduledBuildsStr += "Ticket Number: ${item.ticketNumber}\n"
                        scheduledBuildsStr += "Mode: ${item.mode}\n"
                        scheduledBuildsStr += "Date: ${item.date}\n"
                        scheduledBuildsStr += "Time: ${item.time}\n"
                        scheduledBuildsStr += "Scheduled Build Id: ${item.scheduledBuildId}\n"
                        scheduledBuildsStr += "-----------------------\n"
                    }

                    echo "${scheduledBuildsStr}"
                    
                }
            }
        }
    }
}
