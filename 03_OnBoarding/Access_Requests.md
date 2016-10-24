## Access Requests
As Production Operations holds the keys for most things Production, we also manage access to those things. As the team is not a knowledge base of who employees are, access requests should be submitted by the team lead and should include:

- Specific service the access is requested for (service, app, app environment, repo, etc)
- Level of access required (read-only, read/write, admin, etc)
- Business reason for access
- Who the access is for, including:

	- Full name
	- Email address
	- Team
	- Service specific details such as a Github username

Access is normally granted during the on-boarding process, but there are sometimes things that slip through the cracks.
Here’s a list of everything Ops owns:

| **General** | **Prod Ops Specific** |
|---|---|
| Create | Pager Duty |
| Okta\Okta Preview | Pingdom |
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

* Slack/Jira/Confluence are owned by Eric McGovern, so direct any requests regarding these platforms to him.

* Google Analytics is owned by Asim and the ED&A team, so direct any requests to Asim and his team.

## Create
Create is at the center of access for the Engineering team and remediates access to Github repos and Heroku apps. Any engineer can generally give themselves access to any Heroku app or Github repository via Create by simply looking themselves up and clicking “Edit Access” in order to grant access for themselves.

**Note:** Secured apps are not remediated in the same way, as only those on the Prod Ops team may access secured apps for PCI compliance reasons.

## Okta
Okta is our Single Sign On provider and allows for users and access to be managed from a central location. You can see a list of apps that we have behind Okta here:

[Okta Apps](https://westfieldlabs-admin.okta.com/admin/apps/active)

The most common apps are:

* App Dashboard (deliver.westfield.io)
* Application Console (create.westfield.io)
* AWS Console
* Back Office Console (Production)
* Jenkins (test.westfield.io)
* Jira
* New Relic
* Splunk

Apps that sit behind Okta usually just require users to be assigned to the app, or assigned to a group that already has the app assigned to a group.

## New Relic
To grant access to New Relic, you’ll need to do a few things:

If this is for a new employee/contractor, make sure the user’s email is active and receiving emails. It can take a few hours for IT to provision a new email address.

1. Add the New Relic app in Okta to the user
2. Invite the user via their Westfield email address
3. You’ll need to specify a “Role” which has varying permissions. Most users in New Relic aside from the Ops team are under “Restricted User”.

## Splunk
This is where all of our logs from the various apps and platforms that Labs manages feeds into. Splunk is the central location where engineers can go to run searches on what happened with their apps. Engineers can even set up their own alerts to monitor their apps and address issues when they come up.

Splunk access is delegated via groups in Okta. The currently available Splunk groups are:

* Splunk
* splunk-api
* splunk-cw
* splunk-gpp
* splunk-lbs
* splunk-mobile
* splunk-ops
* splunk-parking
* splunk-r&d
* splunk-wayfinding

These groups have the Splunk app assigned to them already, so all you need to do is assign the group to the user in Okta based on what team they’ll be on. They can then access Splunk at [https://westfieldlabs.splunkcloud.com/en-US/](https://westfieldlabs.splunkcloud.com/en-US/).

## Heroku
This is where all of our APIs are hosted in the cloud. Anything that’s not labeled AEM is usually found here.
Labs has 4 different “buckets” that Production Operations administers:

* westfield-labs
* westfield-secured
* westfield-sandbox
* westfield-development

The main buckets to be concerned about are **westfield-labs** and **westfield-secured**, as the other two are solely for developers to experiment and develop in.

For the **westfield-labs** bucket, engineers can grant themselves access by looking for the app in Create (create.westfield.io) and granting themselves **Contributor** access.

The **westfield-secured** bucket is exactly that, a bucket with access mainly limited to those on the Production Operations team. Any access requests to apps in there will require approval by Peter McInerney. Note that engineers attempting to grant themselves access via Create will find that it does not work.

## Jenkins
This is a core piece of our Continuous Integration pipeline. Jenkins is used to deploy code to both our UAT (staging) and Production environments, as well as running tests against any environment that was deployed to. All engineers should have access so that they can monitor their code deployments.

To grant access:

1. Assign the user the Jenkins app in Okta.
2. Allow the user access in the Global Security matrix under the “Manage Jenkins” section. To get there, go to [https://test.westfield.io/](https://test.westfield.io/) and click “Manage Jenkins” and then “Configure Global Security” to get to the matrix. Add the user’s email address at the bottom (make sure it matches in Okta) and then assign the permissions based on what other users have.

## Hockey App
HockeyApp is a service for app developers to support them in various aspects of their development process, including the management and recruitment of testers, the distribution of apps and the collection of crash reports.

Access is managed by the mobile team, currently being led by Fernando Saragoca. While Ops has access and does own the account, requests should be sent to Fernando for approval. However, if there is a need for Ops to do the access granting, just visit the contacts management page and click `Invite User` there: https://rink.hockeyapp.net/manage/contacts.

## Apigee
Our API management platform. Apigee provides API usage analytics, developer portal and API key management, rate limiting and other tools that help companies improve upon their APIs. Apigee also provides authentication for APIs as well as data transformation.

Nik Linsteadt oversees anything API related and so developer API keys are managed by him. Prod Ops manages everything else, including user access. 

To grant access, go to [https://enterprise.apigee.com/platform/westfieldlabs/](https://enterprise.apigee.com/platform/westfieldlabs/) and click on the `Admin` tab and select `Organization Users` to invite a user by email address. Apigee does support Okta SSO but it is not currently implemented.

## Algolia
This is the provider of the search service that our Center Sites utilize. Algolia provides companies with a platform to implement search capabilities by indexing search items and providing a search box that initiates calls to Algolia’s API to do the heavy lifting so that we don’t have to.

Algolia is owned by Prod Ops but is managed/executed by Nik Linsteadt and the API team. Any access requests must have a justifiable business reason as people that do not understand what they are doing have the potential to do massive damage to our products indexes.

To grant access, log in to [https://www.algolia.com/](https://www.algolia.com/) with the credentials provided in **LastPass**. Note at the top that there are two “Apps” for our `UAT` and `Production` environments, so make sure to select the correct environment for which you want to add a team member to. Once selected, click “Team” from the navigation menu on the left and then the `Add Collaborator` button to add a new member by email. You will need to determine what access is needed, but if it’s an engineer, they’ll need to be able to utilize the Dashboard, Config, Search, and Analytics functions. Billing and API keys permissions should be limited to the Ops team.

**Note:** `search-service-uat` in Heroku uses the Algolia Heroku add-on, which is an entirely different account. This must be accessed from within the `search-service-uat` app dashboard in Heroku.

## Gigya
Gigya is an identity management platform that we use to identify customers via traditional and social registration in order to have a central place to manage data on those customers. Having the data gathered in one central location helps businesses efficiently analyze a customer’s activity online.

Gigya is owned by Prod Ops and used primarily by the API team and various other products at Labs. As there is customer data involved, access requests here must have a justified business value. However, as it seems Labs is not fully utilizing Gigya’s platform, there’s currently an evaluation underway, led by Melanie Dolgachev, to see if there’s any reason to continue usage of this platform.

To log in and grant access, go to [https://console.gigya.com/login.aspx](https://console.gigya.com/login.aspx) and login with your credentials that have admin access. Once logged in, click the `Admin` button on the far right of the menu bar. The default view after a long period of loading should be the `Manage Administrators` interface, showing a current list of “Administrators” and the groups they’re assigned to. Click the `Invite Administrator` button and enter in the email address of the user that needs to be added, then select the group that this Admin needs to be added to. Only Prod Ops and Product leads should be added to the `_admins` group. Click `Invite Administrator` to fire off an invite email to the email address you entered. The new administrator will need to set up their account through that email.

If you need to add the user to multiple groups, have the new admin notify you of account setup completion, then click the edit icon next to their name in order to add additional groups.

## Push.io
This is a service from Oracle Responsys that handles our mobile push notifications. It’s mainly used by our Westfield iOS/Android apps to alert users of sales/deals at a nearby centre. This is an alternative to Mixpanel by Google.

Managed by Prod Ops but used primarily by the Messaging Squad (Lauren Slack Product lead) on the US/UK mobile apps, with most focus on the UK app at the moment.

Approval for access to any region’s app will need to come from Lauren Slack. To grant access, go to [https://manage.pushio.com/](https://manage.pushio.com/) and log in with the `doperations@westfield.com` account. From there, you’ll see a list of apps, 2 for each region, one of which is labeled as a “Dev” app. Click into the app that you want to grant access to in order to see its interface. Then, click the “Team” tab and click the “Invite a Team Member” button, which will pop up an email address field. Enter in the Westfield managed email address for the person you need to grant access to and hit “Invite”. The new user will appear in the Team list and from there, you can click on the newly invited user to edit permission levels, which will need to be provided by Lauren.