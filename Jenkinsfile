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
                    
                    scheduledBuilds.each { item ->
                        
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

                            upcomingBuilds << item

                        }
                        // } else {
                        // //     // The input date is more than 15 minutes in the future
                        // //     println("The input date is more than 15 minutes in the future.")
                        // // }
                        // }
                    }

                    def upcomingBuildsStr = "Upcoming builds:\n"
                    upcomingBuildsStr += "-----------------------\n"
                    upcomingBuilds.each { item ->
                        upcomingBuildsStr += "Environment: ${item.environment}\n"
                        upcomingBuildsStr += "Instance Names: ${item.instanceNames}\n"
                        upcomingBuildsStr += "Ticket Number: ${item.ticketNumber}\n"
                        upcomingBuildsStr += "Mode: ${item.mode}\n"
                        upcomingBuildsStr += "Date: ${item.date}\n"
                        upcomingBuildsStr += "Time: ${item.time}\n"
                        upcomingBuildsStr += "Scheduled Build Id: ${item.scheduledBuildId}\n"
                        upcomingBuildsStr += "-----------------------\n"
                    }
                    
                    echo "${upcomingBuildsStr}"
                }
            }
        }
    }
}
