Zabbix MS Teams AlertScript
========================


About
-----
This is simply a Bash script that uses the custom alert script functionality within [Zabbix](http://www.zabbix.com/) along with the incoming web-hook feature of [MS Teams](https://www.microsoft.com/) 
It is heavily based on the previous Zabbix Slack alert script here: https://github.com/ericoc/zabbix-slack-alertscript

#### Versions
This works with Zabbix 1.8.x or greater - including 2.2, 2.4 and 3.x!

#### Huge thanks and appreciation to:

* [Eric OC](https://github.com/ericoc/) for the original script on which this modification is based 
* [Paul Reeves](https://github.com/pdareeves/) for the hint that Slack changed their API/URLs!
* [Igor Shishkin](https://github.com/teran) for the ability to message users as well as channels!
* Leslie at AspirationHosting for confirming that this script works on Zabbix 1.8.2!
* [Hiromu Yakura](https://github.com/hiromu) for escaping quotation marks in the fields received from Zabbix to have valid JSON!
* [Devlin Gonçalves](https://github.com/devlinrcg), [tkdywc](https://github.com/tkdywc), [damaarten](https://github.com/damaarten), and [lunchables](https://github.com/lunchables) for Zabbix 3.0 AlertScript documentation, suggestions and testing!

Installation
------------

### The script itself

This teams.sh script needs to be placed in the `AlertScriptsPath` directory that is specified within the Zabbix servers' configuration file (`zabbix_server.conf`) and must be executable by the user running the zabbix_server binary (usually "zabbix") on the Zabbix server:

	[root@zabbix ~]# grep AlertScriptsPath /etc/zabbix/zabbix_server.conf
	### Option: AlertScriptsPath
	AlertScriptsPath=/usr/local/share/zabbix/alertscripts

	[root@zabbix ~]# ls -lh /usr/local/share/zabbix/alertscripts/teams.sh
	-rwxr-xr-x 1 root root 1.4K Dec 27 13:48 /usr/local/share/zabbix/alertscripts/teams.sh

If you do change `AlertScriptsPath` (or any other values) within `zabbix_server.conf`, a restart of the Zabbix server software is required.

Configuration
-------------

### MS Teams web-hook

An incoming web-hook integration must be created within your Teams account:

https://msdn.microsoft.com/en-us/microsoft-teams/connectors

![Image of MS Teams Webhook](https://raw.githubusercontent.com/TheMadVanguard/Zabbix/master/MS_Teams_Notification/Images/Teams%20Webhook.png)

### Within the Zabbix web interface

When logged in to the Zabbix servers web interface with super-administrator privileges, navigate to the "Administration" tab, access the "Media Types" sub-tab, and click the "Create media type" button.

You need to create a media type as follows:

* **Name**: Microsoft Teams
* **Type**: Script
* **Script name**: teams.sh

...and ensure that it is enabled before clicking "Save".

However, on Zabbix 3.x and greater, media types are configured slightly differently and you must explicity define the parameters sent to the `team.sh` script. On Zabbix 3.x, three script parameters should be added as follows:

* `{ALERT.SENDTO}`
* `{ALERT.SUBJECT}`
* `{ALERT.MESSAGE}`

![Image Zabbix Media Type](https://raw.githubusercontent.com/TheMadVanguard/Zabbix/master/MS_Teams_Notification/Images/Zabbix%20Media%20Types.PNG)

Then, create a "Microsoft Teams" user on the "Users" sub-tab of the "Administration" tab within the Zabbix servers web interface and specify this users "Media" as the "Microsoft Teams" media type that was just created with the Microsoft Teams channel ("#Zabbix" in the example) or user name (such as "@TheMadVanguard") that you want messages to go to in the "Send to" field as seen below:

![Image of Zabbix Media User](https://raw.githubusercontent.com/TheMadVanguard/Zabbix/master/MS_Teams_Notification/Images/Zabbix%20Media%20User.png)

Finally, an action can then be created on the "Actions" sub-tab of the "Configuration" tab within the Zabbix servers web interface to notify the Zabbix "Microsoft Teams" user ensuring that the "Subject" is "PROBLEM" for "Default message" and "RECOVERY" should you choose to send a "Recovery message".

Keeping the messages short is probably a good idea; use something such as the following for the contents of each message:

	{TRIGGER.NAME} - {HOSTNAME} ({IPADDRESS})

Additionally, you can have multiple different Zabbix users each with "Slack" media types that notify unique Slack users or channels upon different triggered Zabbix actions.

If you are interesting in longer notification messages (with line breaks for example), you may want to reference [this pull request](https://github.com/ericoc/zabbix-slack-alertscript/pull/16) or [any number of forks of this repository](https://github.com/ericoc/zabbix-slack-alertscript/network).

Testing
-------
Assuming that you have set a valid Slack web-hook URL within your "team.sh" file, you can execute the script manually (as opposed to via Zabbix) from Bash on a terminal:

	$ bash teams.sh '@ericoc' PROBLEM 'Oh no! Something is wrong!'

Alerting a specific user name results in the message actually coming from the "slackbot" user using a sort-of "spoofed" user name within the message. A channel alert is sent as you would normally expect from whatever user name you specify in "slack.sh":

![Zabbix Media Test](https://raw.githubusercontent.com/TheMadVanguard/Zabbix/master/MS_Teams_Notification/Images/Zabbix%20Media%20Test.png)
![Zabbix Media Test](https://raw.githubusercontent.com/TheMadVanguard/Zabbix/master/MS_Teams_Notification/Images/Teams%20Test.png)

More Information
----------------
* [Zabbix 2.2 custom alertscripts documentation](https://www.zabbix.com/documentation/2.2/manual/config/notifications/media/script)
* [Zabbix 2.4 custom alertscripts documentation](https://www.zabbix.com/documentation/2.4/manual/config/notifications/media/script)
* [Zabbix 3.x custom alertscripts documentation](https://www.zabbix.com/documentation/3.0/manual/config/notifications/media/script)
* [Zabbix 4.4 custom alertscripts documentation](https://www.zabbix.com/documentation/4.4/manual/config/notifications/media/script)
