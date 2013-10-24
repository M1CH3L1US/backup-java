Smartsheet Account Backup Utility
===

License and Warranty
--------------------
Copyright 2013 Smartsheet.com

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

	http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.


Overview
--------
This command-line backup utility takes a snapshot of the data in a Smartsheet Team or Enterprise account and saves it to local storage.  Only the sheets (including attachments and discussions) owned by users of the account are backed up.

Release History
------------
* Oct 24 2013 - version 1.1: improved logging, improved file download, "continueOnError" option
* Jun 6 2013 - version 1.0

Requirements
------------
* Java Runtime Edition 7 (latest version)
* API access token generated by a Smartsheet account administrator (SysAdmin).  Please see this help article [http://help.smartsheet.com/customer/portal/articles/520100-user-types](http://help.smartsheet.com/customer/portal/articles/520100-user-types) to learn about Smartsheet user types, and [http://www.smartsheet.com/developers/api-faq](http://www.smartsheet.com/developers/api-faq) for instructions on how to generate an API access token.


Limitations
--------
* Only multi-user plans (Team and Enterprise) are supported.  Single-user plans (Basic and Advanced) are not supported at this time.
* Incremental backups are not supported at this time.
* Sheets which have been shared to the members of the Smartsheet account, but are not owned by them, will not be backed up.
* Workspace discussions and attachments are not backed up.

How it works
--------
* The backup utility checks whether the specified output directory already exists.  If it does, the utility renames the directory to include its last modified timestamp, and creates a new output directory.
* For each active user in the account, the backup utility first creates a local directory that matches the user's email address, and then retrieves and backs up all the sheets owned by that user.
* Within each user's directory, the sheets are organized hierarchically (by folders and workspaces) to match the user's Home tab hierarchy in Smartsheet.
* Each sheet is saved as an XLS file that matches the Sheet's name, with discussions saved on a second tab inside the XLS file.  All file attachments are saved in a directory named "[Sheet Name] - attachments".  
* Non-file attachments (Google Drive files, Box files, and web URLs) are stored in a file called "[Sheet Name] - non-file attachments.csv" in the same directory as the file attachments. The following properties are provided for each non-file attachment: Name, URL, and AttachmentType.

Usage
------------
The tool will look for smartsheet-backup.properties file in the current directory (the directory from which it is being executed).
Edit the properties file to set the following parameters:

* "accessToken" (required) - access token that belongs to your Smartsheet account administrator.  See the Requirements section above on how to get an access token
* "outputDir" (required) - desired output directory.  You can provide an absolute * path (e.g., "C:\some\directory") or a path relative to the directory in which the backup utility resides (e.g., "some\directory")
* "zipOutputDir" (optional, default is false) - set to true to create a zipped archive of the outputDir
* "continueOnError" (optional, default is false) - set to true to continue on error (instead of terminating the backup)
* "downloadThreads" (optional, default is 4) - set to desired number of threads used to download attachments

To execute the backup, run the following command "java -jar smartsheet-org-backup.jar".
Since it's a runnable jar, you don't need to set classpath or copy other jars. Everything you need is in the runnable jar.


Logging
------------------
* All the messages, including what is backed up as well as the folders and files being created, are logged to stdout.
* A divider line is logged for each active user backed up by the tool.  Inactive users are skipped.
* All the waits and retries resulting from 503 Service Unavailable errors are logged.
* All asynchronous download requests as well as the attachments downloaded are logged.
* The tool exits with exit code 0 on success, and -1 on failure.
* Errors are prepended "\*\*\*ERROR\*\*\*" and logged.
* Upon successful completion (no errors), the tool will output a brief summary including "\*\*\* Org backup done... \*\*\*" along with the number of users backed up and time elapsed.
* If the "continueOnError" option is enabled, the last line will instead be a summary of the number of errors encountered, plus the path to an error log file.
The error log file will contain timestamped logs of each error, with details provided to allow you to attempt manual recovery of a specific user's sheet or attachment skipped in the backup, or to simply try running the tool again.  

Contributing
---
The source code for this utility is available on Github at [https://github.com/smartsheet-platform/backup-java](https://github.com/smartsheet-platform/backup-java). If you want to contribute a fix or an enhancement, please fork the Github repository and create a pull request with your changes.

[![githalytics.com alpha](https://cruel-carlota.pagodabox.com/fb84ff8823ccfb788e524e4ce734af64 "githalytics.com")](http://githalytics.com/smartsheet-platform/backup-java)
