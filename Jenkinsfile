// import java.time.LocalDateTime
// import java.time.format.DateTimeFormatter
// import java.time.temporal.ChronoUnit

class scheduledBuild {
    String environment
    String instanceNames
    String ticketNumber
    String mode
    String date
    String time
    Int secondsFromNow
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
                    //def jsonFile = readFile 'Test.json'
                    def jsonFile = readFile 'C:\\code\\AMICreationQueueService\\Test.json'
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
                        
                        executionDateTimeStr = item.date + ' ' + item.time

                        Date executionDateTime = null

                        try {
                            // Parse the future date and time
                            def dateFormat = new java.text.SimpleDateFormat("MM/dd/yyyy HH:mm")
                            executionDateTime = dateFormat.parse(executionDateTimeStr)
                        }
                        catch (ex) {
                            // Handle the error without failing the build
                            unstable("Unable to parse DateTime ${executionDateTimeStr}.")
                        }
                        
                        // Get the current date and time
                        Date currentDate = new Date()

                        // Calculate the difference in milliseconds
                        long differenceInMillis = executionDateTime.time - currentDate.time

                        // Convert the difference to seconds
                        int secondsFromNow = differenceInMillis / 1000

                        item.secondsFromNow = secondsFromNow

                        if (secondsFromNow <= 0 || secondsFromNow < 900) {

                            upcomingBuilds << item

                        }
                    }

                    if (upcomingBuilds.isEmpty()) {
                         echo 'No upcoming builds identified.'
                        currentBuild.result = 'SUCCESS'
                        return
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
                        upcomingBuildsStr += "SecondsFromNow: ${item.secondsFromNow}\n"
                        upcomingBuildsStr += "Scheduled Build Id: ${item.scheduledBuildId}\n"
                        upcomingBuildsStr += "-----------------------\n"
                    }

                    echo "${upcomingBuildsStr}"
                }
            }
        }
        // stage('QueueBuildsForExecution') {
        //     steps {
        //         script {
        //         }
        //     }
        // }
    }
}
