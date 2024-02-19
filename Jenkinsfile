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
    String amiCreationRequestId
}

def queueAMICreation(amiCreationRequestId, account, instanceNames, instanceIDs, region, ticketNumber, mode,  date, time, secondsFromNow) {
    // def job = Hudson.instance.getJob('AMICreationPipeline')
    def job = Jenkins.instance.getItemByFullName('AMICreationPipeline')

    if (job == null) {
        throw new IllegalStateException("Job not found: AMICreationPipeline")
    }

    def params = [
        new StringParameterValue('Account', account),
        new StringParameterValue('InstanceNames', instanceNames),
        new StringParameterValue('InstanceIDs', instanceIDs),
        new StringParameterValue('Region', region),
        new StringParameterValue('TicketNumber', ticketNumber),
        new StringParameterValue('Mode', mode),
        new StringParameterValue('Date', date),
        new StringParameterValue('Time', time),
        new StringParameterValue('AmiCreationRequestId', amiCreationRequestId)
    ]

    def future = job.scheduleBuild2(secondsFromNow, new ParametersAction(params))
}

def amiCreationRequestDB = []
def scheduledAMICreations = []
def upcomingAMICreations = [] // AMI Creation Requests scheduled less than 15 minutes from now

pipeline {
    agent any

    triggers {
            // This cron syntax triggers the job every 15 minutes
            cron('H/15 * * * *')
    }

    stages {
        stage('ReadJSON') {
            steps {
                script {

                    def queueFilePath = 'C:\\code\\AMICreationQueueService\\Test.json'

                    def existingContent = new File(queueFilePath).text
                    def jsonSlurperClassic = new JsonSlurperClassic()

                    if (existingContent.length() == 0) {
                        error("The queue file is empty. Exiting the pipeline.")
                    }

                    // Try to parse the existing content, handle potential parsing errors
                    try {
                        amiCreationRequestDB = jsonSlurperClassic.parseText(existingContent)
                    } catch (Exception e) {
                        error ("Unable to parse json file. Please check for syntax errors.")
                    }
                    
                }
            }
        }
        // stage('GetUpcomingAMICreationRequests') {
        //     steps {
        //         script {
                    
        //             scheduledAMICreations = amiCreationRequestDB.findAll { it.Status == 'Scheduled' }

        //             // def scheduledAMICreationsStr = "Scheduled builds:\n"
        //             // scheduledAMICreationsStr += "-----------------------\n"
        //             scheduledAMICreations.each { item ->

        //                 //     String instanceNames = item.AMIs.collect { it.instanceDetails.instanceName }.join(',')
        //                 //     String instanceIds = item.AMIs.collect { it.instanceDetails.instanceId }.join(',')

        //                 //     scheduledAMICreationsStr += "AMI Creation Request Id: ${item.AmiCreationRequestId}\n"
        //                 //     scheduledAMICreationsStr += "Account: ${item.Account}\n"
        //                 //     scheduledAMICreationsStr += "Instance Names: ${instanceNames}\n"
        //                 //     scheduledAMICreationsStr += "Instance IDs: ${instanceIds}\n"
        //                 //     scheduledAMICreationsStr += "Region: ${item.Region}\n"
        //                 //     scheduledAMICreationsStr += "Ticket Number: ${item.TicketNumber}\n"
        //                 //     scheduledAMICreationsStr += "Mode: ${item.Mode}\n"
        //                 //     scheduledAMICreationsStr += "Date: ${item.Date}\n"
        //                 //     scheduledAMICreationsStr += "Time: ${item.Time}\n"
        //                 //     scheduledAMICreationsStr += "SecondsFromNow: ${item.SecondsFromNow}\n"
        //                 //     scheduledAMICreationsStr += "-----------------------\n"
        //                 // }

        //                 // echo "${scheduledAMICreationsStr}"
                        
        //                 executionDateTimeStr = item.Date + ' ' + item.Time

        //                 Date executionDateTime = null

        //                 try {
        //                     // Parse the future date and time
        //                     def dateFormat = new java.text.SimpleDateFormat("MM/dd/yyyy HH:mm")
        //                     executionDateTime = dateFormat.parse(executionDateTimeStr)
        //                 }
        //                 catch (ex) {
        //                     // Handle the error without failing the build
        //                     unstable("Unable to parse DateTime ${executionDateTimeStr}.")
        //                 }
                        
        //                 // Get the current date and time
        //                 Date currentDate = new Date()

        //                 // Calculate the difference in milliseconds
        //                 long differenceInMillis = executionDateTime.time - currentDate.time

        //                 // Convert the difference to seconds
        //                 int secondsFromNow = differenceInMillis / 1000

        //                 item.SecondsFromNow = secondsFromNow

        //                 if (secondsFromNow <= 0 || secondsFromNow < 900) {

        //                     upcomingAMICreations << item

        //                 }
        //             }

        //             if (upcomingAMICreations.isEmpty()) {
        //                  echo 'No upcoming builds identified.'
        //                 currentBuild.result = 'SUCCESS'
        //                 return
        //             }

        //             def upcomingAMICreationsStr = "Upcoming AMI Creations:\n"
        //             upcomingAMICreationsStr += "-----------------------\n"
        //             upcomingAMICreations.each { item ->

        //                 String instanceNames = item.AMIs.collect { it.instanceDetails.instanceName }.join(',')
        //                 String instanceIds = item.AMIs.collect { it.instanceDetails.instanceId }.join(',')

        //                 upcomingAMICreationsStr += "AMI Creation Request Id: ${item.AmiCreationRequestId}\n"
        //                 upcomingAMICreationsStr += "Account: ${item.Account}\n"
        //                 upcomingAMICreationsStr += "Instance Names: ${instanceNames}\n"
        //                 upcomingAMICreationsStr += "Instance IDs: ${instanceIds}\n"
        //                 upcomingAMICreationsStr += "Region: ${item.Region}\n"
        //                 upcomingAMICreationsStr += "Ticket Number: ${item.TicketNumber}\n"
        //                 upcomingAMICreationsStr += "Mode: ${item.Mode}\n"
        //                 upcomingAMICreationsStr += "Date: ${item.Date}\n"
        //                 upcomingAMICreationsStr += "Time: ${item.Time}\n"
        //                 upcomingAMICreationsStr += "SecondsFromNow: ${item.SecondsFromNow}\n"
        //                 upcomingAMICreationsStr += "-----------------------\n"
        //             }

        //             echo "${upcomingAMICreationsStr}"
        //         }
        //     }
        // }
        // stage('QueueAMICreationRequestsForExecution') {
        //     steps {
        //         script {
        //             upcomingAMICreations.each { item ->

        //                 String instanceNames = item.AMIs.collect { it.instanceDetails.instanceName }.join(',')
        //                 String instanceIds = item.AMIs.collect { it.instanceDetails.instanceId }.join(',')

        //                 queueAMICreation(item.AmiCreationRequestId, item.Account, instanceNames, instanceIds, item.Region, item.TicketNumber, item.Mode, item.Date, item.Time, item.SecondsFromNow)
        //             }
        //         }
        //     }
        // }
        stage('SyncPendingAMIStatus') {
            steps {
                script {
                    def requestsWithPendingAMIs = amiCreationRequestDB.findAll { it.Status == 'AwaitingAvailability' }

                    // Group by a composite key of 'category' and 'value' range
                    def groupedByAccountAndRegion = requestsWithPendingAMIs.groupBy {
                        // Creating a tuple (as a List) of 'category' and a custom range for 'value'
                        // For simplicity, categorizing 'value' into '<=30' and '>30'
                        [it.Account, it.Region]
                    }

                    // Print the result
                    groupedByAccountAndRegion.each { key, value ->
                        // println("Key: $key, Values: $value")
                        accountNumber = key[0]
                        region = key[1]

                        value.each { AMICreationRequest ->
                            echo "${AMICreationRequest.AmiCreationRequestId}"
                        }
                    }

                    // def requestsGroupedByAccount = requestsWithPendingAMIs.groupBy { it.Account, it.Region }
                    
                    // requestsGroupedByAccount.each { request ->
                    //     echo "${request}"
                    // }

                    // def requests = validInstances.groupBy { it.region }
                    // requestsWithPendingAMIs.each { request ->
                    //     AMIs = request.AMIs

                    //     role = 'AMICreationRole'
                    //     region = request.Region
                    //     account = request.Account

                        // AMIs.each { AMI ->
                        //     withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'rod_aws']]) {
                        //         withAWS(role: role, region: region, roleAccount: account, duration: '3600' ){
                                
                        //         def awsCliCommand = "aws ec2 describe-instances --filters \"Name=tag:Name,Values=${instanceNamesStr}\" --region ${region} --output json"

                        //         // Executes the AWS CLI command and does some post-processing.
                        //         // The output includes the command at the top and can't be parsed so we have to drop the first line
                        //         def cliOutput = bat(script: awsCliCommand, returnStdout: true).trim()
                        //         cliOutput = cliOutput.readLines().drop(1).join("\n")

                        //         // Parse the CLI output as JSON
                        //         def jsonSlurper = new groovy.json.JsonSlurper()
                        //         def cliOutputJson = jsonSlurper.parseText(cliOutput)
                        //         }
                            
                        //     }
                        // }
                }
            }
        }
    }
}



