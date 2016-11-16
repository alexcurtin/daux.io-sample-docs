# User Audit Exports

### NOTE: Never remove a user under a `doperations` email alias, as this is the account owner that Ops uses for master control.

For the monthly audit, there are 5 systems that Ops does not own but helps manage. However, Ops doesn't need to perform the actual audits, only obtain a list of all current users that have access, then send them off to the owners of those services. These services are:

* Slack - scripted
* Hockey App - scripted
* Jira - scripted
* Confluence
* Google Analytics

There are Ruby scripts for retrieving users for some of these systems, located in the [User Audit Scripts](https://github.com/westfield/user_audit_scripts) repo.

**Notes:**

* As the scripts require some API keys and other credentials, you'll need an `.env` file in order to have the proper authorization. You can get this from Calvin.
* All scripts should be run from the root of the repo, as the scripts are hardcoded to generate `.csv` files and place them into the `/CSV` folder.
* `.csv` files have the naming convention of `script_name_yyyy-mm-dd.csv`. Ex. `slack_users_2016-11-02.csv`

## Slack

Owner: Eric McGovern, Director Program Management

Slack has been locally managed for a long time and only recently placed behind Okta SSO. However, the implementation is not complete, so user provisioning is not correct yet.

To obtain a list of all currently active Slack members, you can use the `slack_users.rb` script in the [User Audit Scripts](https://github.com/westfield/user_audit_scripts) repo. This script uses the [Slack API Gem](https://github.com/aki017/slack-ruby-gem) to access our Westfield Labs Slack account for administration.

Run `ruby slack_users.rb` from the root of the repo in your terminal. Look for the generated CSV file in the `/CSV` folder and send that off to the owner for auditing.

## Hockey App

Owner: Fernando Saragoca, Lead Mobile Developer

Hockey App users can be retrieved via the `hockeyapp_users.rb` script. Note that the Hockey App API didn't have an endpoint for listing all users, so the script grabs all the users associated with all the apps in the account, then only lists the unique users into the CSV file.

Run `ruby hockeyapp_users.rb` from the root of the repo in your terminal. Look for the generated CSV file in the `/CSV` folder and send that off to the owner for auditing.

## Jira

Owner: Eric McGovern, Director Program Management

Jira users can be retrieved with the `jira_users.rb` script. The Atlassian APIs use pagination, so the script will run for a while as each page is a new request.

Run `ruby jira_users.rb` from the root of the repo in your terminal. Look for the generated CSV file in the `/CSV` folder and send that off to the owner for auditing.

## Confluence

Owner: Eric McGovern, Director Program Management

Unfortunately, Atlassian didn't provide a proper method of retrieving a users list, so there's currently no script for retrieving those users. Instead:

1. Log in to [Confluence](https://wiki.westfieldlabs.com/) with an Admin account.
2. Click the `Settings` cog at the top right of the page to open a drop down menu.
3. Click `User Management` to load a page that allows you to search for users.
4. From here, click `Show all users` located under the search box
5. Look towards the right of the page for a `Show: 10 20 50 100`. Click the `100` in order to show 100 users per page.
6. With your mouse, copy all users starting with the first name in the top row.
7. Open Excel and paste these users in.
8. Go back to the Confluence page and click the `Next` button at the bottom of the list of users to load the next page of users.
9. Repeat steps 6 - 8 until there are no more users to copy.
10. Copy the column headers and insert them into the top row of the list you've accumulated in Excel.
11. Save the worksheet with the same naming convention as the other systems, in this case: `confluence_users_yyyy-mm-dd.xls`
12. Send this list off to Eric McGovern.

## Google Analytics

Owner: Asim Mohammad, Vice President ED&A

No script written to automate user retrieval yet.

1. Log in to [Analytics](https://analytics.google.com/analytics/web) with your Google Apps user under your Westfield email. It's important to note that if your user isn't granted Admin access, then you won't be able to retrieve the user list.
2. If you do not see all 4 folders, then you do not have the necessary admin privileges:

	* Westfield Digital Business
	* Westfield Labs
	* Westfield UK
	* Westfield US

3. Click the `Admin` tab towards the top.
4. 3 columns will load. Under `Account`, you should see `Westfield Digital Business`. Under that, click `User Management`. The other 2 columns will disappear, replaced by a list of users assigned to this property.
6. Look for the `Show rows` drop down, click it, then select `500`, which should be the max number of users you can display on the page.
7. Copy all users in that list into an Excel worksheet.
8. Delete the column of row numbers and the column of `Delete` links.
9. Make sure to include the column headers and above those, the name of the property/folder that those users belong to.
10. Look back at the first column under `Account`. This time, click the drop down menu under that and select the next property/folder to load the list of users for that one.
11. Repeat steps 6 - 10 until users for all 4 properties/folders have been copied into the worksheet.
12. Save the file with the naming convention of: `google_analytics_users_yyyy-mm-dd.xls`
13. Send this list off to Asim and Wayne Coburn, copying Peter McInerney and Alex Lee.