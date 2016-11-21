# Auditing Miscellaneous Systems

### NOTE: Never remove a user under a `doperations` email alias, as this is the account owner that Ops uses for master control.

## Google Search Console

Log in to [Google Search Console](https://www.google.com/webmasters/tools/home) with your Google Apps account that has `Owner` privileges on the main properties. There are only 8 properties here to be concerned with:

* http://www.westfield.com
* https://www.westfield.com
* http://uk.westfield.com
* https://uk.westfield.com
* http://www.westfield.com.au
* https://www.westfield.com.au
* http://www.westfield.co.nz
* https://www.westfield.co.nz

Click the `Manage Property` button to the far right of your chosen property to open up a drop dowwn, then click `Add or remove users`. This will load a list of users that have access to that property's data.

Go through each property's list of users and compare them against the [Systems Access Audit Sheet](https://docs.google.com/a/westfield.com/spreadsheets/d/1GGN0d8D2vJlg17qP-zEVvWvfcBEfOjmn40QTnVFrhSU/edit?usp=sharing) in the `Google Search Console` worksheet tab. There, you'll find 8 lists of users, 1 for each property. Users that do not have a Westfield email address are highlighted yellow and users that have been removed are highlighted gray with a notation of the date they were removed.

If there are any additions of users to Google Search Console, add them to the Audit sheet. Once done, send the sheet to the PMs of the **Web Squad** and have them go through the list to determine if anyone should be removed.

Make sure to track changes in the sheet so that we have a reference as to who was removed and when.

## Google Apps

This is a bit of a tricky one, as Google Apps hasn't had any formal process for management of access until recently. The current ruleset is that you must have a Westfield administered email alias in order to be added to Google Apps, and if you want access to any Google App, like Google Analytics, you must be administered through Google Apps.

As Google Apps is not limited to Westfield Labs users, this means that Okta is not reliable as a source of truth for whether an employee is still a part of Westfield. Vendors/Agencies can also be given access via Google Apps but usually under a `nwe.vendor/agencyname`@westfield.com alias.

So, to audit Google Apps, you'll need to run the email address against Okta to see if you can find the user and check if the user is still active. If you cannot locate the user in Okta, it means the user has never logged in to Okta. You can try searching [Zendesk](https://westfieldlabs.zendesk.com/agent/search/) for the access request ticket.

A user should only be removed if the user:

* Does not exist in Okta or no Zendesk ticket request for access
* Was deprovisioned from Okta

If a user meets one of these conditions, click the three dots icon to the far right of the user's name and select `Suspend`, as the data/files can be restored if a mistake was made in suspending the user. Once you confirm the suspension, the user disappears, but you can click the `Filters` button to filter for `Active Users`, `Suspended Users`, and `Recently Deleted Users`. We can use this to track changes.

## Papertrail

Papertrail is almost deprecated at this point as we're using Splunk for logging now, but it's still a good idea to check. There should only be a handful of non-Ops users that have access that are comprised of engineers in ED&A.

Log in to [Papertrail](https://papertrailapp.com/) and click the `Settings` button at the top right. Then click `Members` to load the user list. Run each email address against Okta to make sure that they are still Active and if not, click the `Remove` button and confirm the deletion.

## Algolia

**Note:** There are 2 Algolia accounts, one via Algolia.com and another through a Heroku add-on for the `search-service-uat` app. This must be accessed from within the `search-service-uat` app dashboard in Heroku.

Only a few people have access to Algolia, and most, if not all, are on the API Squad. To audit, 

### Algolia.com Instance

To audit, log in to the [Algolia Dashboard](https://www.algolia.com/dashboard) with the `doperations` user (credentials in Lastpass). Note at the top that there are two “Apps” for our `UAT` and `Production` environments, which are basically treated as individual apps. Each app will have its own assigned Team so you'll need to make sure that you **audit both**.

Click `Team` on the left menu navigation bar to load a list of email addresses showing permissions assigned. Run each email address against Okta to make sure that no one is inactive and remove if they are. Also make sure that no one has permissions for `Billing`, `API Keys`, or `Team`, as these permissions should only be limited to Ops members via the `doperations` account.

### Heroku search-service-uat Instance

To audit, log in to your Heroku account and look up the `search-service-uat` app. From the `Overview` tab, you should see the `Algolia Realtime Search` add-on. Click that to load the Algolia dashboard for the app. There's only one app here, so you don't have to worry about multiple apps.

However, as you can see, access to this Algolia dashboard isn't limited to the users listed on the `Team` page. If you have access to the Heroku app, you'll have access to the Algolia instance. This means that you'll need to run the email addresses of the people that have access in Heroku, against Okta, to make sure that they're still active. This is normally taken care of by an audit of Heroku access but it's never a bad idea to check anyways.

## Apigee

Auditing Apigee is simple. Just log in to our enterprise [Apigee](https://enterprise.apigee.com/platform/westfieldlabs/) account, then hover over the `Admin` button at the top menu bar to expand the drop down menu. Then click `Organization Users`.

Go through the list of users and make sure that anyone with a Westfield alias for their email is still active in Okta. Anyone with an Apigee email address can be ignored.

If you find someone that is inactive, go ahead and click the `Remove` button to the far right of their row and confirm their deletion.

Make sure to track a list of removed users in the [Systems Access Audit Sheet](https://docs.google.com/a/westfield.com/spreadsheets/d/1GGN0d8D2vJlg17qP-zEVvWvfcBEfOjmn40QTnVFrhSU/edit?usp=sharing) under the `Apigee` worksheet tab.

## Gigya

Log in to [Gigya Console](https://console.gigya.com/Site/partners/dashboard.aspx) using your Admin account and click the `Admin` button at the far right of the menu bar. It might take a while but a list of users should evenutally appear. Note that Gigya labels users as `Administrators` so don't let that throw you off. Permission levels are delegated by groups so Admins are members of an `_admins` groups

Go through the list of users and make sure that anyone with a Westfield alias for their email is still active in Okta. Anyone with a Gigya email address can be ignored.

If you find someone that is inactive, go ahead and click the red `X` button to the far right of their row and confirm their deletion.

Make sure to track a list of removed users in the [Systems Access Audit Sheet](https://docs.google.com/a/westfield.com/spreadsheets/d/1GGN0d8D2vJlg17qP-zEVvWvfcBEfOjmn40QTnVFrhSU/edit?usp=sharing) under the `Gigya` worksheet tab.

## Lastpass

If you have the Lastpass Chrome extension, click the Lastpass icon in your extension menu and then click `Admin Console`. Otherwise, go to [https://lastpass.com](https://lastpass.com/) and click `Login` and login with your credentials, then look at the gray left side menu and look for the briefcase icon for the `Admin Console`.

Once the admin dashboard has loaded, click `USERS` on the left side menu and then click `Users` in the drop down. This will load a list of all users that have access to Lastpass.

You'll most likely only be concerned with the `Status` column. If you see anyone that's `Disabled`, you can ignore. 

For `Administrators` or blank entries in the `Status` column:

#### Westfield Aliases

Run their email addresses against Okta to make sure that they are still active.

If you find an inactive user, click the `Choose` drop-down menu and select `Disable User` and confirm the disabling.

#### Vendors

If the email address looks like it belongs to a vendor, figure out who manages the vendor relationship and consult with them on whether or not Lastpass is still needed for that vendor. For example, the vendor ParkIQ is managed by Jeff Pittelkau so he would be the one to speak with in regards to whether we need to disable that access.

## Push.io

Go to [https://manage.pushio.com/](https://manage.pushio.com/) and log in with the `doperations@westfield.com` account. From there, you’ll see a list of apps, 2 for each region, one of which is labeled as a “Dev” app. Click into the app that you want to audit in order to see its interface. Then, click the “Team” tab to load the list of users that can access that app. Email addresses here should be Westfield aliases only. Run each email against Okta to make sure that they are still active.

If there is a user that is inactive in Okta, you can click into the user in the Push.io console and click `Remove User` to remove the user **from that app**. You'll need to go through all of the 8 apps in order to make sure that the user has been removed entirely from Push.io.

Make sure to track a list of removed users and apps removed from in the [Systems Access Audit Sheet](https://docs.google.com/a/westfield.com/spreadsheets/d/1GGN0d8D2vJlg17qP-zEVvWvfcBEfOjmn40QTnVFrhSU/edit?usp=sharing) under the `Push.io` worksheet tab.


## WP Engine

## Spreedly

Spreedly doesn't have a way in its UI to remove users, so the current solution is to email Spreedly support at support@spreedly.com and give them the email addresses for the users that need to be removed.

Make sure to track a list of removed users in the [Systems Access Audit Sheet](https://docs.google.com/a/westfield.com/spreadsheets/d/1GGN0d8D2vJlg17qP-zEVvWvfcBEfOjmn40QTnVFrhSU/edit?usp=sharing) under the `Spreedly` worksheet tab.