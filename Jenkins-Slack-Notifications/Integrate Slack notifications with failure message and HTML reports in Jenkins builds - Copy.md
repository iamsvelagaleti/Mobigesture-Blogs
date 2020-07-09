# Integrate Slack notifications with failure message and HTML reports in Jenkins pipeline builds

Slack, a highly accepted instant messaging platform across the  enterprises. It is not just limited to messaging, but also used as a notification delivery channel for system monitoring. To use the notification services in Slack, Jenkins provides us a Slack plugin to send developer designed pipeline job notifications. To achieve it, there are several ways, out of all we will provide you with one solution.

Jenkins pipeline integrates CI and CD steps together and performs all activities build, deploy and test in single job. Jenkins pipeline runs  every bash command/ script command/batch command as a separate process independently unlike a sequence of steps together. Because of this reason, whenever any single command fails inside a step, Jenkins pipeline immediately aborts and returns failure. This is the reason that we cannot implement customized error messages after a build failure. I am going to provide a solution to overcome this problem and can implement customized error messages which later can be used to send in Slack notification and inside HTML reports.

## Creating a slack channel and integrate Jenkins application

In your Slack application, you will see a section **Channels** besides it, click on the + symbol to create the channel.

<img src="D:\Medium Documents\Slack\1_create_channel.png" alt="image-20200405195638180" style="zoom:40%;" />

Add the required people to this channel. You can skip this step for now and can do later if needed.

<img src="D:\Medium Documents\Slack\2_add_people.png" alt="image-20200405200032154" style="zoom:40%;" />



Click on ***Add an app*** link to add Jenkins app to this channel

<img src="D:\Medium Documents\Slack\3_add_an_app.png" alt="image-20200405200553414" style="zoom:40%;" />

This will open a page where you need to select the ***Jenkins CI*** and click ***install***.

<img src="D:\Medium Documents\Slack\4_add_an_app_Jenkins.png" alt="image-20200405200801954" style="zoom:40%;" />



Click ***Add to Slack*** 

<img src="D:\Medium Documents\Slack\5_Add_to_slack.png" alt="6" style="zoom:40%;" />



In the next page, select the channel name "#test-ci-alerts" from dropdown and click "*Add Jenkins CI Integration"*

In the next page, go to ***Integration Setting*** section (down to the page). Channel name and Token are shown. Copy the *Token* to somewhere ( This is required to integrate the Jenkins with your channel in Jenkins UI). 

<img src="D:\Medium Documents\Slack\6_integration_settings.png" alt="9" style="zoom:40%;" />

With this step, Slack Channel creation and adding Jenkins application is completed.



## Jenkins configuration 

#### Install Slack and HTML publisher plugins

Got to *Manage Jenkins ->Plugin Manager* and install the below 2 plugins *Slack Notification plugin* and *HTML Publisher Plugin*, if they are not already installed.

<img src="D:\Medium Documents\Slack\7_Slack_plugin.png" alt="image-20200406143200564" style="zoom:50%;" />

<img src="D:\Medium Documents\Slack\8_html_publish_plugin.png" alt="image-20200406143428055" style="zoom:50%;" />



#### Integrate Slack channel into Jenkins

Go to *Manage Jenkins -> Configure system* . Scroll down to *Slack* section as shown below.



![image-20200406150645683](D:\Medium Documents\Slack\9_Jenkins_slack_config.png)

Provide Workspace name of your team in which the Slack channel is created. An example is given in above screenshot.

In *Credential* input, we need to configure the Slack token ID. Click on the Add button and then click on Jenkins dropdown which will redirect to Credentials configuration section of Jenkins as shown below.

<img src="D:\Medium Documents\Slack\10_Slack_token.png" alt="image-20200406150937547" style="zoom:50%;" />

Here configure the credentials. 

​		Select *Global* credentials as Domain

​		*Secret text* as Kind.

​		For Scope select *Global*

​		In *Secret* input, provide the Token id generated when Jenkins app is added to Slack (refer the section of Slack Channel creation above)

​		Provide any name for *ID* and *Description*. 

Click on the *Add* button. It will redirect back to *Slack* section of *System configuration*.

From the dropdown select the configured credential (the description of token credential is shown in the dropdown) and save the configuration. 

<img src="D:\Medium Documents\Slack\11_Jenkins_slack_config2.png" alt="image-20200406160233381" style="zoom:50%;" />

With this step, Jenkins is integrated to send Slack notifications to the configured Slack channel.



## Creation of Jenkinsfile with Customized error message, Slack notification and Publish HTML reports

#### Define customized error messages

As explained beginning of this document, Jenkins pipeline returns exit whenever there is a failure in any of the command. Later steps cannot be executed in Jenkins job and the errors cannot be caught for exception handling. But providing an error message for each failure always helps developers in which steps the build is failed and is easy to debug. For this, we can define any variable in the beginning of the every step with proper error message. This variable will be used in preparing reports for sending Slack notification and HTML report. 

Below code snippet explains the same case.

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



*CI_ERROR* is a variable can be defined wherever required before beginning of any command. I have defined in *SCM Checkout* stage . If my build fails in this step, it notifies with this error message included in the Slack message explained below. 

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

An HTML report provides more readability and visibility of the build status. It can be included with any of the columns like Job name, build number date, status, etc. To simplify creating HTML report, I have created an HTML template file with some placeholders which can be replaced for every build in Post section. The sample HTML file *report.html* is attached in Gist ( https://gist.github.com/rameshmaddi-git/decd9adb8a6f3bf3811dff698fc58ec5#file-report-html ) . 

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

<img src="D:\Medium Documents\Slack\12_HTML_report_link.png" alt="image-20200406165415767" style="zoom:50%;" />

When it is clicked it generates below report.

<img src="D:\Medium Documents\Slack\13_HTML_report_display.png" alt="image-20200406165540836" style="zoom:50%;" />

Below is the snapshot of Slack messages one is for successful build and other is for failure build.

<img src="D:\Medium Documents\Slack\14_Final_slack_message.png" alt="image-20200406165708170" style="zoom:50%;" />

As said earlier, Error description hold customized error message and Build report points to the HTML report as shown in the above screen shot.