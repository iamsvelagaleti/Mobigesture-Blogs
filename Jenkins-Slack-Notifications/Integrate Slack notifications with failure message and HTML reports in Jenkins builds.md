[`Slack`](https://slack.com), a highly accepted instant messaging platform across the  enterprises. It is not just limited to messaging, but also used as a notification delivery channel for system monitoring. Slack integrate with many apps. You can find a list of Slack apps [here](https://slack.com/apps).                                       `     

[`Jenkins`](https://jenkins.io)  pipeline runs  every bash command/ script command/batch command as a separate process independently unlike a sequence of steps together. Because of this reason, whenever any single command fails inside a step, Jenkins pipeline immediately aborts and returns failure. This is the reason that we cannot implement customized error messages after a build failure. I am going to provide a solution to overcome this problem and can implement customized error messages which later can be used to send in Slack notification and inside HTML reports.

Create a slack channel and add Jenkins App to this channel which can be used in our Pipeline job for sending notifications.  Slack provides a token when Jenkins App is added to the channel. Configure the token and the channel in Jenkins. For more help on this, check the below link:

https://medium.com/appgambit/integrating-jenkins-with-slack-notifications-4f14d1ce9c7a

In the next steps I am going to explain how to configure Slack notifications and add HTML reports in a Jenkins Pipeline job.

## Jenkins configuration 

For demonstration purpose, I have created channel called  *#test-ci-alerts* and added the details in Jenkins. Here is the screenshot after configuration (Please refer to the above link for configuration of slack channel)

![Jenkins Slack configuration](https://github.com/iamsvelagaleti/Mobigesture-Blogs/blob/master/Jenkins-Slack-Notifications/1_Jenkins_slack_config2.png?raw=true)

#### Install HTML publisher plugin

Got to *Manage Jenkins ->Plugin Manager* and install  *HTML Publisher Plugin*, if it is not already installed.

<img src="https://github.com/iamsvelagaleti/Mobigesture-Blogs/blob/master/Jenkins-Slack-Notifications/1_html_publish_plugin.jpg" alt="image-20200406143428055" style="zoom:30%;" />



#### Define customized error messages in *Jenkinsfile*

Since after any step fails, Jenkins pipeline will switch to Post section to execute post build steps. We cannot catch the failure and define an error for it. To overcome this, define the error message of any step at the beginning of the steps. For example in the below code, CI_ERROR variable is defined with an error message before actual code of ***SCM Checkout***. With this, for any reason if the step fails, *CI_ERROR* variable holds that error message since it is already defined. This variable needs to be redefined in every step so that it has proper error message with respective to that step. 

```
stage ('SCM Checkout') {
		steps {
			script {
				CI_ERROR = "Failed while checking out SCM"
       				... ### Code for SCM Checkout
			}
		}
	}
```

The sample *Jenkinsfile* is placed in my Gist linked below.

https://gist.github.com/rameshmaddi-git/decd9adb8a6f3bf3811dff698fc58ec5

In Post section, *CI_ERROR* can be set to null in case of Successful build like below.

```
if (currentBuild.currentResult == 'SUCCESS') {
					CI_ERROR = "NA"
					}
				}
```

#### Publish HTML reports

Generally Post section inside *Jenkinsfile* is used to perform post activities after failure/success of the build. Publish HTML report is Post action and needs to be defined in this section. 

##### HTML report file

An HTML report provides more readability and visibility of the build status. It can be included with any of the columns like Job name, build number date, status, etc.  One can also use the link of HTML report within a slack notification so that it can be directly viewed by clicking on the link. To simplify creating HTML report, I have created an HTML template file with some placeholders which can be replaced for every build in Post section. The sample HTML file *report.html* is attached in Gist ( https://gist.github.com/rameshmaddi-git/decd9adb8a6f3bf3811dff698fc58ec5#file-report-html ) . 

Below is the sample HTML code with place holders.

```
<thead>
			<tr>
				<th>Job Name</th>
				<th>Build No</th>
				<th>Date</th>
				<th>Build Status</th>
				<th>Error Description</th>
				<th>Console Output</th>
			</tr>
		</thead>
		<tbody>
			<tr>
				<td>%%JOBNAME%%</td>
				<td>%%BUILDNO%%</td>
				<td>%%DATE%%</td>
				<td>%%BUILD_STATUS%%</td>
				<td>%%ERROR%%</td>
				<td><a href="%%CONSOLE_LOG%%">Click Here</a></td>
			</tr>
		</tbody>
```

*JOBNAME, BUILDNO, DATE, BUILD_STATUS, ERROR* and *CONSOLE* log are place holders. This html file needs to be pushed along with *Jenkinsfile* to SCM repo. Whenever, Jenkins pipeline build runs, it pulls this html file along with *Jenkinsfile*.

##### publishHTML section:

Before creating *publishHTML* report section, use bash commands to replace placeholders with actual values. 

A sample *sed* command to report JOBNAME placeholder.

```
sed -i "s/%%JOBNAME%%/${env.JOB_NAME}/g" report.html
```

Now add *publishHTML* section

```
publishHTML(target:[
					allowMissing: true,
					alwaysLinkToLastBuild: true, 
					keepAll: true, 
					reportDir: "${WORKSPACE}", 
					reportFiles: 'report.html', 
					reportName: 'CI-Build-HTML-Report', 
					reportTitles: 'CI-Build-HTML-Report'
					])
```

Report name and report titles can be any names. Report file should the name of the HTML file. This will generate an HTML report with the report title.

##### Slack message section:

Include Slack message in Post section.

Slack message needs to be defined before using it in the *sendSlack* function. I have created a user defined function and calling it from Post section. This function needs to be kept outside of the *pipeline* section and needs to be called immediately after *publishHTML* report. Refer to the Gist https://gist.github.com/rameshmaddi-git/decd9adb8a6f3bf3811dff698fc58ec5 for entire sample code.

*buildSummary* is the message prepared with Job, status, Error description and Build Report. Build Report column is for HTML report. The report title needs to be concatenated with *BUILD_URL* to form build report URL as shown in the code snippet.

```
def sendSlackNotifcation() 
{ 
	if ( currentBuild.currentResult == "SUCCESS" ) {
		buildSummary = "Job:  ${env.JOB_NAME}\n Status: *SUCCESS*\n Build Report: ${env.BUILD_URL}CI-Build-HTML-Report"

		slackSend color : "good", message: "${buildSummary}", channel: '#test-ci-alerts'
		}
	else {
		buildSummary = "Job:  ${env.JOB_NAME}\n Status: *FAILURE*\n Error description: *${CI_ERROR}* \nBuild Report :${env.BUILD_URL}CI-Build-HTML-Report"
		slackSend color : "danger", message: "${buildSummary}", channel: '#test-ci-alerts'
		}

}
```

With all these steps, creation of *Jenkinsfile* is completed and it can be pushed to SCM. 

Now Create a Pipeline job (In my Example it is Build Application). 

Once the Job is run an HTML report link is created in Jenkins Job for each run/build as shown in the below which always point to the last build.

<img src="https://github.com/iamsvelagaleti/Mobigesture-Blogs/blob/master/Jenkins-Slack-Notifications/2_HTML_report_link.jpg" alt="image-20200406165415767" style="zoom:20%;" />

When it is clicked it generates below report.

<img src="https://github.com/iamsvelagaleti/Mobigesture-Blogs/blob/master/Jenkins-Slack-Notifications/3_HTML_report_display.jpg" alt="image-20200406165540836" style="zoom:30%;" />

Below is the snapshot of Slack messages one is for successful build and other is for failure build.

<img src="https://github.com/iamsvelagaleti/Mobigesture-Blogs/blob/master/Jenkins-Slack-Notifications/4_Final_slack_message.jpg" alt="image-20200406165708170" style="zoom:30%;" />

Error description hold customized error message and Build report points to the HTML report as shown in the above screen shot.