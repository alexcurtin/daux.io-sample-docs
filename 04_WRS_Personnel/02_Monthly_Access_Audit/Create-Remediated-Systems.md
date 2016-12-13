# Auditing Create Remediated Systems

### NOTE: Never remove a user under a `doperations` email alias, as this is the account owner that Ops uses for master control.

## Create

Create is our source of truth for all of Westfield Labs Engineering. Any Engineer, whether Full Time or Contracting, will need to be added to Create in order to gain access to our Github repos and Heroku applications. As Create sits behind Okta, the Engineer will first need a Westfield email address set up via Westfield Corporate IT so that they exist in Active Directory, which remediates out to Okta, which then allows for the Create app to be provisioned for the Engineer and grants access to the app. However, the user still needs to be manually added to Create in order for any access to be provisioned. This is normally handled by Business Support as a default task during the onboarding process.

When a user is added to or removed from Create, they are also granted/denied access to Github and Heroku based on what team and access level they are assigned within Create. This is what we mean by Create remediating out to Github and Heroku. It usually takes anywhere from 30 seconds to a few minutes for this remediation to take effect.

Auditing Create is largely dependent on Okta, as there is no other reference for us to know whether or not a user should still exist within Create. If a person is not deprovisioned in Okta, we have no way of knowing if the person should be removed from Create.

As a member of the Ops team, you should have Admin access to [Create](https://create.westfield.io/) by default. Once the page has loaded, click `People` from the left menu bar to load a list of people. You'll need to look up each name in Okta to see whether or not the person is still an active employee/contractor with Westfield Labs.

If the person is deprovisioned in Okta, go to the [Systems Access Audit Sheet](https://docs.google.com/a/westfield.com/spreadsheets/d/1GGN0d8D2vJlg17qP-zEVvWvfcBEfOjmn40QTnVFrhSU/edit?usp=sharing) which should load the Create worksheet by default. Move any engineer that was terminated, into the `Terminated` column towards the bottom, highlight that cell `red`, then enter the date that this engineer was audited. It's not elegant but it's a way to keep track of who was removed, as Create currently doesn't have any functionality for record keeping.

**Note**: You might also find it prudent to note down the user's Github username, which you can find if you click into the user in Create. This will be essential for auditing Github, as not all users have their name as part of their Github username.

Finally, go back to Create and click the red trash can icon to the far right of this person's name and confirm the deletion. This action should deprovision a user from our Github org which also deletes any forks of Westfield repos, as well as removing the user from our Heroku orgs.

## Github

While Github is remediated by Create, it's still a good idea to check Github to make sure that anyone that was removed from Create no longer has access to our org. The main rule of Github access is that if you don't exist in Create, you shouldn't exist in the Github org.

To audit, go to the [Westfield Github Org - People](https://github.com/orgs/westfield/people) page. The default view should be the `Members` list, which lists everyone that has been invited to be a member of the organization. You'll need to look through the list and try to find the matching person in Create. You can click into the person's profile in Create in order to find their Github username for cross-referencing.

The next to go through is the list of `Collaborators`, which normally consist of vendors/contractors and PMs/Designers within Westfield Labs. This is the same idea here as far as auditing goes: find the owner of the username in Create and remove the user if they don't exist in Create.

All changes should be tracked in the [Systems Access Audit Sheet](https://docs.google.com/a/westfield.com/spreadsheets/d/1GGN0d8D2vJlg17qP-zEVvWvfcBEfOjmn40QTnVFrhSU/edit?usp=sharing) under the `Github` worksheet tab.

## Heroku

Heroku, while also remediated by Create, is also best double checked for parity's sake. During the onboarding process, Engineers are invited to our Heroku orgs only via their Westfield managed email addresses. There are some **legacy** cases where contractors have access via an email address outside of Westfield, but those are getting whittled down as of late. For those cases, at the very least, they'll have that email address in Okta and they should be listed under that same email address in Create.

To audit, log in to the [Heroku Dashboard](https://dashboard.heroku.com/apps) and click the drop down menu at the top left, usually labeled `Personal Apps`. This will allow you to select the organization you'd like to administer. Westfield Labs has 4 orgs:

* westfield-development
* westfield-labs
* westfield-sandbox
* westfield-secured

Create only remediates out to the `westfield-labs` organization for most Engineers, and the `westfield-secured` organization for members of the Operations/Production Engineering team. The remaining two orgs, `development` and `sandbox`, are administered manually on a case-by-case basis so they're not remediated by Create, but also shouldn't include anyone that doesn't exist in Create.

Once you've selected your org, look for the `Access` tab and click it to load a list of users that have access to that org. Go through the list of users to make sure that the users you've removed are actually removed, and that there are no other stray users that do not also exist in Create.

All changes should be tracked in the [Systems Access Audit Sheet](https://docs.google.com/a/westfield.com/spreadsheets/d/1GGN0d8D2vJlg17qP-zEVvWvfcBEfOjmn40QTnVFrhSU/edit?usp=sharing) under the `Heroku` worksheet tab.

## Jenkins

Jenkins is not remediated by Create in the sense that Github and Heroku are, but if a user does not exist in Create, they should not exist in Jenkins either. Technically, Jenkins is remediated by Okta, however, we usually want to make sure that Engineers in Westfield Labs are the only ones that can access our Jenkins instance, which is why we use Create as a reference.

User management is handled by an `Access Matrix` within the Jenkins configuration. Go to [Jenkins](https://test.westfield.io/) and click the `Manage Jenkins` button in the left side navigation menu, then click `Configure Global Security`. Scroll to the bottom until you see a grid/matrix. The first column should be a list of email addresses. Go through that list and make sure that those email addresses exist in Create and are active in Okta. If the user is deprovisioned from Okta or does not exist in Create, they should be removed from the matrix. Scroll all the way to the right to find a red `X` button. Click that and then click `Save`/`Apply` for the change to take effect.

All changes should be tracked in the [Systems Access Audit Sheet](https://docs.google.com/a/westfield.com/spreadsheets/d/1GGN0d8D2vJlg17qP-zEVvWvfcBEfOjmn40QTnVFrhSU/edit?usp=sharing) under the `Jenkins` worksheet tab.