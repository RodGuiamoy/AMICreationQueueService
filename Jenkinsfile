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

void sendEmailNotification (Object AMICreationRequest) {
    // def message = ""

    // if (AMICreationRequest.Status == "AwaitingAvailability") {
    //     message = "AMI(s) have been successfully created in AWS environment ${AMICreationRequest.Environment}. Another email will be sent once the AMIs are on Available status. Reference ticket: ${AMICreationRequest.TicketNumber}"
    // }
    // else if ((AMICreationRequest.Status == "Scheduled") || (AMICreationRequest.Status == "QueuedForExecution")) {
    //     message = "An AMI creation request has been scheduled to be executed in ${AMICreationRequest.Date} ${AMICreationRequest.Time} in AWS environment ${AMICreationRequest.Environment}. Reference ticket: ${AMICreationRequest.TicketNumber}"
    // }
    
    def body = """
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
    body {
        font-family: Arial, sans-serif;
        margin: 0;
        padding: 0 20px;
        box-sizing: border-box;
    }
    table {
        width: 100%;
        border-collapse: collapse;
        margin: 25px 0;
        font-size: 0.9em;
        min-width: 400px;
        border-radius: 5px 5px 0 0;
        overflow: hidden;
        box-shadow: 0 0 20px rgba(0, 0, 0, 0.15);
    }
    thead tr {
        background-color: #005B9A;
        color: #ffffff;
        text-align: left;
    }
    th, td {
        padding: 12px 15px;
    }
    tbody tr {
        border-bottom: 1px solid #dddddd;
    }

    tbody tr:nth-of-type(even) {
        background-color: #f0f0f0; /* Light gray for better readability */
    }

    tbody tr:last-of-type {
        border-bottom: 2px solid #005B9A;
    }

    tbody tr.active-row {
        font-weight: bold;
        color: #005B9A;
    }
    .status-message {
        margin-top: 20px;
        font-size: 0.9em;
        color: #333; /* Dark gray for the message */
    }
    .fine-print {
        margin-top: 10px;
        font-size: 0.6em;
        color: #333; /* Dark gray for the message */
    }
</style>
</head>
<body>

<p class="status-message">AMIs now available. Reference ticket: ${AMICreationRequest.TicketNumber}</p>


<table>
    <thead>
        <tr>
            <th>Region</th>
            <th>Instance ID</th>
            <th>InstanceName</th>
            <th>AMI ID</th>
            <th>AMI Name</th>
            <th>Status</th>
        </tr>
    </thead>
    <tbody>
"""
                        AMICreationRequest.AMIs.each { AMI ->
                            body += """
        <tr>
            <td>${AMI.instanceDetails.region}</td>
            <td>${AMI.instanceDetails.instanceId}</td>
            <td>${AMI.instanceDetails.instanceName}</td>
            <td>${AMI.amiId}</td>
            <td>${AMI.amiName}</td>
            <td>${AMI.status}</td>
        </tr>
                            """
                        }

                        // Close the table
                        body += """
    </tbody>
</table>

<p class="fine-print">AMI Creation Request Id: ${AMICreationRequest.AmiCreationRequestId}</p>
<p class="fine-print">Requester: ${AMICreationRequest.User}</p>

</body>
</html>
""" 

                        // Send the email using the email-ext plugin, including the table
                        emailext(
                            subject: "AMI Creation Report",
                            body: body,
                            mimeType: 'text/html',
                            to: 'recipient@example.com'
                        )
}

def amiCreationRequestDB = []
def scheduledAMICreations = []
def upcomingAMICreations = [] // AMI Creation Requests scheduled less than 15 minutes from now
def requestsWithPendingAMIs = [] 

def amiCreationRequestDBPath = 'C:\\code\\AMICreationQueueService\\Test.json'

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

                    

                    def existingContent = new File(amiCreationRequestDBPath).text
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
        stage('RetrieveAMIStatus') {
            steps {
                script {
                    requestsWithPendingAMIs = amiCreationRequestDB.findAll { it.Status == 'AwaitingAvailability' }

                    // Group by a composite key of 'category' and 'value' range
                    def groupedByAccountAndRegion = requestsWithPendingAMIs.groupBy {
                        // Creating a tuple (as a List) of 'category' and a custom range for 'value'
                        // For simplicity, categorizing 'value' into '<=30' and '>30'
                        [it.Account, it.Region]
                    }

                    // Print the result
                    groupedByAccountAndRegion.each { key, value ->

                        def account = key[0]
                        def region = key[1]
                        def role = 'AMICreationRole'

                        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'rod_aws']]) {
                            withAWS(role: role, region: region, roleAccount: account, duration: '3600' ){
                                value.each { AMICreationRequest ->
                                    
                                    def AMIs = AMICreationRequest.AMIs
                                    def amiCreationRequestId = AMICreationRequest.AmiCreationRequestId
                                    def amiIDsStr = AMIs.collect { it.amiId }.join(' ')
                                    def awsCliCommand = "aws ec2 describe-images --image-ids ${amiIDsStr} --output json"

                                    // Executes the AWS CLI command and does some post-processing.
                                    // The output includes the command at the top and can't be parsed so we have to drop the first line
                                    def cliOutput = bat(script: awsCliCommand, returnStdout: true).trim()
                                    cliOutput = cliOutput.readLines().drop(1).join("\n")

                                    // Parse the CLI output as JSON
                                    def jsonSlurper = new groovy.json.JsonSlurper()
                                    def cliOutputJson = jsonSlurper.parseText(cliOutput)

                                    // echo "${cliOutputJson}"
                                     // Check if 'Reservations' is empty
                                    if (cliOutputJson.Images.isEmpty()) {
                                        unstable("Unable to retrieve AMI status for AMI Creation Request ID ${amiCreationRequestId}.")
                                        return
                                    }

                                    cliOutputJson.Images.each { image ->
                                        echo "${image.ImageId} ${image.State}"
                                        def amiDBRecord = AMIs.find { it.amiId == image.ImageId }
                                        amiDBRecord.status = image.State
                                    }

                                    def amisStillOnPending = AMIs.findAll { it.status != "available" }
                                    if (amisStillOnPending.isEmpty()) {
                                        echo "AMI Creation Request ID ${amiCreationRequestId} complete."
                                        AMICreationRequest.Status = "Completed"
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }
        stage('SyncAMIStatusToDB') {
            steps {
                script {
                    // Convert the list back to JSON string and pretty print it
                    def newJsonStr = JsonOutput.toJson(amiCreationRequestDB)
                    def prettyJsonStr = JsonOutput.prettyPrint(newJsonStr)

                    // Write the JSON string back to the file
                    writeFile(file: amiCreationRequestDBPath, text: prettyJsonStr)
                }
            }
        }
        stage('SendEmailNotifications') {
            steps {
                script {
                    recentlyCompletedRequests = requestsWithPendingAMIs.findAll { it.Status == 'Completed' }

                    if (recentlyCompletedRequests.isEmpty()) {
                        echo "No recently completed requests."
                    }
                    else {
                        recentlyCompletedRequests.each { amiCreationRequestObj ->
                            sendEmailNotification(amiCreationRequestObj)
                        }
                    }

                }
            }
        }
    }
}



