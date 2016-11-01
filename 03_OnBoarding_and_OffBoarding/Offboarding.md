## OffBoarding Employees and Contractors

As the Production Engineering/Operations team manages access for most of the many services/platforms/systems that Westfield Labs uses, it also falls on this team to remove access for employees and contractors that have either quit, been terminated, or have expired contract terms.

Unfortunately, this team is rarely ever notified of when an employee/contractor needs access removed. For that, we have the [Monthly Audit](). However, for the cases where we do know that someone is leaving, we do have to make sure to action on those offboarding requests.

Usually, a request will come in via [help@westfieldsupport.com](help@westfieldsupport.com). The request ends up in Zendesk, where the Business Support team will determine that the request needs to go to the Prod Eng/Ops team. The request will end up in Jira under the Production Operations Team project here: [POT Support Board](https://jira.westfieldlabs.com/secure/RapidBoard.jspa?rapidView=986&view=detail&selectedIssue=POT-2096).

When the request is received, look for the action date of when the employee will be leaving. Make sure to only take action on that date, usually at the end of the day. Note that if the termination of employment is not amicable, access should be terminated **immediately** so as to prevent any malicious retaliation.

### Ops owned systems:
| **General** | **Prod Ops Specific** |
|---|---|
| Create | Pager Duty |
| Okta/Okta Preview | Pingdom |
|Slack (Eric McGovern)| CSC Global |
| New Relic | Dynect |
| Papertrail (deprecated) | StatusPage.io |
| Splunk | Nagios |
| JIRA (Eric McGovern) | AWS |
| Confluence (Eric McGovern) | VPN |
| Github | IPA |
| Heroku | Fastly |
| Jenkins | DigiCert |
| Hockey App (Fernando Saragoca) |  |
| Service Now |  |
| Lastpass |  |
| Apigee |  |
| Google Apps |  | 
| Google Analytics (ED&A Asim/Wayne) |  |
| Google Search Console (Temporarily managed by Ops) |  |
| Google Developer Console |  |
| iTunes |  |
| Algolia |  |
| Gigya |  |
| Push.io |  |
| Jibestream UXM |  |

### Actioning an offboarding request

Thankfully, most of the offboarding can be handled by Okta and Create, as we do have a lot of services currently sitting behind Okta SSO. Deactivate a user in Okta and they'll no longer have access to the apps that require authentication through Okta. Create will remediate out to Github and Heroku, removing access to Github repos and Heroku apps once the user is deleted from Create. However, you'll still need to check both the Github Westfield organization, as well as the Heroku bucket access list, to make sure that the person is removed.

The rest of the systems you'll have to handle manually, by logging in to the Admin accounts for those systems and deleting the terminated employee. Make sure to go through each of the systems thoroughly, as we're never sure who may have access to something that they normally wouldn't have.

**Okta:** Log in to the [Okta Admin Console](https://westfieldlabs-admin.okta.com/admin/dashboard) and look up the employee that you want to deactivate. Click into that user to view user details. Once the page loads, you'll see a `More Actions` button towards the top right. Click that to open a drop-down menu, then click `Deactivate`. This will prevent the user from being able to log in to any apps/systems that require Okta authentication.

Apps sitting behind Okta SSO:

* All Labs created apps (Create, Deliver, Concierge, etc.)
* Slack
* New Relic
* Splunk
* Jira
* Confluence
* Jenkins

**Create:** Log in at [create.westfield.io](https://create.westfield.io/). Once logged in, click `People` in the left side navigation menu to bring up a list of all users. The default view should drop you into the `Filter` field, where you should be able to begin typing the name of the employee that needs to be removed from Create. Once the name comes up, you'll notice a trash can icon to the far right of the name. Click that and confirm deletion of the user in order to remove from Create, and thereby removing access to Github and Heroku.

- **Note:** Remember to grab the Github username before deleting the user, so that you can double check that the person was removed from the Github org. Some people were added before Create was developed to remediate, so those users' Github and Heroku access wouldn't be remediated by Create.

**New Relic:** Though it sits behind Okta, still requires manually deleting the user out of the account. Log in to [New Relic](https://rpm.newrelic.com/accounts/10589) here, which should drop you into the `Account Settings` page. There, you'll see a list of Active Users and a `Filter` box, which you can use to enter in the user's email address or name to locate the user. To the right of the name, click on the Trash can icon to remove from our New Relic account. Removing from the main `westfield-labs` account will remove access to all of our sub-accounts as well.

**Splunk:** As Splunk is entirely behind Okta SSO, nothing needs to be done on Splunk to offboard someone. Once their Okta account is deprovisioned, they will not be able to access Splunk. However, it's important to note that the record of the user is still listed as a provisioned account in Splunk. This list will get cleaned up at some point in the future.

**JIRA/Confluence:** Users need to be deactivated here even after deprovisioning in Okta. Note that you will not be able to delete a user from JIRA, as the system prevents users from being deleted if they've got open issues. Deactivating will be sufficient to prevent further login. Confluence is remedied by JIRA so if you deactivate a user in JIRA, it deactivates in Confluence as well.

**Jenkins:** Deprovisioning a user in Okta will prevent access, but for the sake of parity, you should still go into [https://test.westfield.io/](https://test.westfield.io/) and remove the user from the access matrix. Click `Manage Jenkins` on the left side menu bar, then click `Configure Global Security`. This page will take slightly longer to finish loading, but once it does, locate the email address for the user (`CMD + F` helps a lot here) and then scroll all the way to the right and click the corresponding `X` to delete the user. Make sure to hit `Save` or `Apply` for these changes to take effect.

**Hockey App:** Log in to [rink.hockeyapp.net/manage/dashboard](https://rink.hockeyapp.net/manage/dashboard) and click the `Teams` tab, then click `Contacts` and use the Search box to filter for the user needing removal from the system. Click into the user and then click the `Remove User` button located at the top left of the screen.

**LastPass:** Assuming you have the LastPass Chrome extension, click on the LastPass button in your browser and click `Admin Console` in the drop down menu to load the Admin console page. Then click the `Users` tab and use the Search box to locate the user. Click on the `Choose` button to open up the drop down menu and look for the appropriate action to take, which are:

* Disable User
* Remove user from company
* Delete User

**Apigee:** Log in to [Apigee](https://enterprise.apigee.com/platform/#/westfieldlabs/) with an account that has Admin priveleges, then click the `Admin` tab, which will by default bring you to the `Organization Users` list. Use the Search box to filter for the user to remove, then click the `Remove` button to the right of that name.

**Google Apps:** Log in to your Admin account with [Google Apps](https://admin.google.com/us.westfield.com/AdminHome). Click the `Users` button to load a list of all users, then filter for the user that needs to be removed. Click into that user and then click the 3 dot icon towards the top left of that user's profile card to bring up a drop down menu. Select `Suspend` or `Delete`, whichever is more appropriate. If you choose to delete, you'll have the option to transfer docs and data over to another user. This should usually be the employee/contractor's direct report.

**Google Analytics:** Log in to your Admin account with [Google Analytics](https://analytics.google.com). You should see all 4 accounts that Westfield Labs administers:

1. Westfield Digital Business
2. Westfield Labs
3. Westfield UK
4. Westfield US

If you don't see those accounts, you'll need to either log in to the Production Operations account, or have someone else that has access to all 4 accounts do the offboarding.

Click the `ADMIN` tab and then click on `User Management` under the `ACCOUNT` column, which should be Westfield Digital Business (first account). This will load a list of users on the right. Use the Search box to filter for the user you want to remove. Click the `delete` button to the right of the user's info and confirm the deletion. If the user does **not** show up in this account, it means they were never assigned permissions for this account.

To move to the next account, in the column on the left under `ACCOUNT`, click to open up a drop down menu and select the next account. Repeat for each account until you've ensured that the user is removed from all 4 accounts.

**Google Search Console:** Log in to [Google Search Console](https://www.google.com/webmasters/tools) with your Admin credentials. Depending on what account you're logged in with (WDB or your own), you may see a lot of properties listed in the account. You'll only need to be concerned with (notice the HTTP vs HTTP**S**):

- https://www.westfield.com
- http://www.westfield.com
- https://www.westfield.com.au
- http://www.westfield.com.au
- https://www.westfield.co.nz
- http://www.westfield.co.nz
- https://uk.westfield.com
- http://uk.westfield.com

To the right of any of these properties, you should see a `Manage Property` button. Click that to open a drop down menu and then click `Add or remove users`. This will load a page that lists users that can view details for that particular property and what their permissions are. Click on the user that you need to remove and a `Delete` button will appear on the right. Click that and confirm the removal from the property.

If the person being removed is actually an Owner, then you'll need to hit the `Manage property owners` link and click the `Unverify` button for the owner you'd like to remove. **Never remove westfield.digital.business@gmail.com** as this is the owner of the Search Console account.

**Google Play Developer Console:** Log in to [Google Play Dev Console](https://play.google.com/apps/publish/) with the `westfield.mobile.android@gmail.com` account, password is stored in Lastpass. Only the master account can add/remove users.

Click the `Settings` cog icon on the left menu nav bar, then click `User accounts & rights` to bring up a list of users that have access to the console. On the far right, click the `Settings` cog that relates to the user that needs to be removed, then click `Remove user from console` and confirm.

**iTunes:** Need to figure out which account

**Algolia:** Log in to [Algolia](https://www.algolia.com/dashboard) using the `doperations` account that Lastpass will suggest for you. Once you're at the dashboard after logging in, look towards the top for either the word `PRODUCTION` or `UAT` next to `APPS`. We have two apps under the Production and UAT names and each have their own "Team".

Click the `Team` button from the left side nav bar, then click the `Revoke` button for the team member you wish to remove from Algolia and confirm. Make sure to switch to the other app (UAT/Production) to remove the user from that app as well.

- **Note:** `search-service-uat` in Heroku uses the Algolia Heroku add-on, which is an entirely different account. This must be accessed from within the `search-service-uat` app dashboard in Heroku.

**Gigya:** To log in and grant access, go to [https://console.gigya.com/login.aspx](https://console.gigya.com/login.aspx) and login with your credentials that have admin access. Once logged in, click the Admin button on the far right of the menu bar. The default view after a long period of loading should be the Manage Administrators interface, showing a current list of “Administrators” and the groups they’re assigned to. Locate the user that needs to be removed and then click the red `X` button to the far right of that name and confirm the deletion.

**Push.io:** Go to [https://manage.pushio.com/](https://manage.pushio.com/) and log in with the `doperations@westfield.com` account. From there, you’ll see a list of apps, 2 for each region, one of which is labeled as a “Dev” app. Click into the app that you want to revoke access to in order to see its interface. Then, click the “Team” tab and click the appropriate user. Then click the `Remove User` button and confirm. Repeat for each app that you need to remove the user from.

**Jibestream UXM:** To revoke access, first, determine if you need to hit UAT or Production. Then, visit either [https://maps.uat.westfield.io/](https://maps.uat.westfield.io/) or [https://maps.westfield.io/](https://maps.westfield.io/). Log in with the Admin credentials located in Lastpass. Once logged in, click on the `Permissions` tab on the left side navigation menu to administer users. You'll see two sections, `Users` and `Roles`. You'll be looking for the user in the `Users` section by scrolling through that section. However, note that you'll also need to select the centre to administer, which is a drop down menu located at the top of the page. Different centres have different users assigned to them.

Once you've found your user, click on the user to select the user (a gray background will appear) and then click the `Remove User` button.