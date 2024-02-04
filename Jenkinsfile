import groovy.json.JsonSlurperClassic
import groovy.json.JsonOutput

class scheduledBuild {
    String environment
    String instanceNames
    String ticketNumber
    String mode
    String date
    String time
    int secondsFromNow
    String scheduledBuildId
}

def queueAMICreation(environment, instanceNames, ticketNumber, mode, scheduledBuildId, date, time, secondsFromNow) {
    // def job = Hudson.instance.getJob('AMICreationPipeline')
    def job = Jenkins.instance.getItemByFullName('AMICreationPipeline')

    if (job == null) {
        throw new IllegalStateException("Job not found: AMICreationPipeline")
    }

    def params = [
        new StringParameterValue('Environment', environment),
        new StringParameterValue('InstanceNames', instanceNames),
        new StringParameterValue('TicketNumber', ticketNumber),
        new StringParameterValue('Mode', mode),
        new StringParameterValue('Date', date),
        new StringParameterValue('Time', time),
        new StringParameterValue('ScheduledBuildId', scheduledBuildId)
    ]

    def future = job.scheduleBuild2(secondsFromNow, new ParametersAction(params))
}

def scheduledAMICreations = []
def upcomingAMICreations = [] // AMI Creation Requests scheduled less than 15 minutes from now

pipeline {
    agent any

    stages {
        stage('ReadJSON') {
            steps {
                script {
                    // Assuming the JSON file is named 'data.json' and located in the workspace
                    //def jsonFile = readFile 'Test.json'
                    // def jsonFile = readFile 'C:\\code\\AMICreationQueueService\\Test.json'
                    // def jsonData = new groovy.json.JsonSlurper().parseText(jsonFile)

                    // // Loop through each item in the JSON array
                    // jsonData.each { item ->
                    //     scheduledAMICreations << new scheduledBuild(environment: item.environment,  instanceNames: item.instanceNames, ticketNumber: item.ticketNumber, mode: item.mode, date: item.date, time: item.time, scheduledBuildId: item.scheduledBuildId)
                    // }

                    def queueFilePath = 'C:\\code\\AMICreationQueueService\\Test.json'

                    def existingContent = new File(queueFilePath).text
                    def jsonSlurperClassic = new JsonSlurperClassic()

                    // Try to parse the existing content, handle potential parsing errors
                    try {
                        scheduledAMICreations = jsonSlurperClassic.parseText(existingContent)
                    } catch (Exception e) {

                        error ("Unable to parse json file. Please check for syntax errors.")
                        
                    }
                    
                }
            }
        }
        stage('GetUpcomingAMICreationRequests') {
            steps {
                script {
                    
                    scheduledAMICreations.each { item ->
                        
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

                            upcomingAMICreations << item

                        }
                    }

                    if (upcomingAMICreations.isEmpty()) {
                         echo 'No upcoming builds identified.'
                        currentBuild.result = 'SUCCESS'
                        return
                    }

                    def upcomingAMICreationsStr = "Upcoming builds:\n"
                    upcomingAMICreationsStr += "-----------------------\n"
                    upcomingAMICreations.each { item ->
                        upcomingAMICreationsStr += "Environment: ${item.environment}\n"
                        upcomingAMICreationsStr += "Instance Names: ${item.instanceNames}\n"
                        upcomingAMICreationsStr += "Ticket Number: ${item.ticketNumber}\n"
                        upcomingAMICreationsStr += "Mode: ${item.mode}\n"
                        upcomingAMICreationsStr += "Date: ${item.date}\n"
                        upcomingAMICreationsStr += "Time: ${item.time}\n"
                        upcomingAMICreationsStr += "SecondsFromNow: ${item.secondsFromNow}\n"
                        upcomingAMICreationsStr += "Scheduled Build Id: ${item.scheduledBuildId}\n"
                        upcomingAMICreationsStr += "-----------------------\n"
                    }

                    echo "${upcomingAMICreationsStr}"
                }
            }
        }
        stage('QueueAMICreationRequestsForExecution') {
            steps {
                script {
                    upcomingAMICreations.each { item ->
                        // Example usage
                        queueAMICreation(item.environment, item.instanceNames, item.ticketNumber, item.mode, item.scheduledBuildId, item.date, item.time, item.secondsFromNow)
                    }
                }
            }
        }
    }
}

