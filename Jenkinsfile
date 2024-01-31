import java.time.LocalDateTime
import java.time.format.DateTimeFormatter
import java.time.temporal.ChronoUnit

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
def upcomingBuilds = []

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

                        executionDateStr = item.date + ' ' + item.time

                        // Get the current date and time
                        LocalDateTime currentDateTime = LocalDateTime.now()

                        // Define the format pattern for your input date and time
                        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM/dd/yyyy HH:mm")

                        // Parse the input date and time (replace this with your input)
                        LocalDateTime inputDateTime = LocalDateTime.parse(executionDateStr, formatter)

                        // Calculate the time difference in minutes
                        long minutesDifference = ChronoUnit.MINUTES.between(currentDateTime, inputDateTime)

                        if (minutesDifference <= 0 || minutesDifference < 15) {
                            // The input date is earlier than or within 15 minutes of the current time
                            println("The input date is earlier than or within 15 minutes of the current time.")
                        } else {
                            // The input date is more than 15 minutes in the future
                            println("The input date is more than 15 minutes in the future.")
                        }
                    }

                    echo "${scheduledBuildsStr}"
                    
                }
            }
        }
    }
}
