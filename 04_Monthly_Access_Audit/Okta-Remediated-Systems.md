# Auditing Okta Remediated Systems

### NOTE: Never remove a user under a `doperations` email alias, as this is the account owner that Ops uses for master control.

## Okta
Okta is remediated by Active Directory, making Okta and AD our source of truth for whether or not an employee or contractor should exist in any system managed by Westfield Labs. However, this makes auditing Okta a bit complicated as there are many Westfield employees that get automatically provisioned because of "Just-In-Time" provisioning.

JIT provisioning means that if a Westfield employee attempts to log in to a Westfield Labs app that sits behind Okta, Okta will perform a lookup with Active Directory to see if there is an existing account with the email address used to log in. If there is, Okta will import those account details if there isn't already an existing account under that email address.

To audit Okta, you'll need the [User Audit Scripts](https://www.github.com/westfield/user_audit_scripts) repo. Run the `okta_users.rb` script, from the root of the repo,to grab a list of all users from the Okta API. The script will generate 3 CSV files:

* okta_users - Okta mastered users
* ad_users - Active Directory mastered users
* other_users - Users mastered through another service

**Notes:**

* All CSV files are generated into a `/csv` folder
* The `ad_users` CSV file that gets generated can be ignored during the audit, as any removed users that are mastered by Active Directory will simply get re-provisioned when they attempt to log back in.

Once the script has completed, run the `sort_local_users.rb` script (also from root of repo), to sort the Okta mastered users into Westfield and non-westfield users, generating:

* local\_wf\_users
* local\_non\_wf\_users

The `local_wf_users` CSV will contain all Okta mastered accounts that use a Westfield email address (us.westfield.com, westfield.com, scentregroup.com, westfield-uk.com). There really shouldn't be anything in here as all users that have a Westfield email address should be mastered by Active Directory. If there are entries, figure out why this is the case and get them re-mastered by Active Directory.

The `local_non_wf_users` CSV should contain a list of users that are Okta mastered and do not end in a Westfield email address. These often belong to retailers and contractors/consultants. Figure out who these users report to and find out whether or not they are still in use. Elise Crossman is a good resource for retailers and Scott Szarapka/Nigel Pepper/Eunice Yuen are good resources for contracting engineers.

## New Relic

While New Relic sits behind Okta, there is still a manual process that involes inviting the user via email to our New Relic account. Deprovisioning the user from Okta or just the New Relic app in Okta should prevent access, but it's always good to just go through the list of users that have access to New Relic, for parity and in the event of an edge case that allows a local login.

To view the list of users, log in to your admin account at the [New Relic Account Settings](https://rpm.newrelic.com/accounts/10589) page. This should immediately drop you in to the `Account Settings` page where you should be able to view a list of Pending/Active users. Go through each user here and compare against Okta to see if:

1. The user exists under that email address
2. Is the user still active

If a user doesn't exist in Okta or is deactivated, you'll need to remove the user from New Relic. Go back to the user in New Relic and look for the trash can icon to the far right of the user's name/email address. Click that and confirm the deletion.

## Splunk

Splunk is remediated entirely by Okta, so there is no way for a user to log in to Splunk locally, though there are locally created accounts for certain purposes such as a dashboard user or index manager. There are also locally created Splunk users that belong to Splunk employees. These may need to be audited at some point, but there hasn't been any discussion surrounding this yet.

To view all users, go to the [Splunk Cloud User Management](https://westfieldlabs.splunkcloud.com/en-US/manager/wflabs_users/authentication/users) page, which should either prompt you to log in via Okta or use your current session to sign you in. Once in, you should see a comprehensive table of users. You'll only need to be concerned with:

| Username | Authentication System | Full Name | Email Address |
| -------- | --------------------- | --------- | ------------- |

**Username**: Currently is using the email address pulled from Okta.

**Authentication System**: Two options, SAML or Splunk. SAML means remediated via Okta while Splunk means the user was locally created.

**Full Name**: Pulls email address from Okta. There are cases where the email address field isn't properly populated in the Okta response, so the name will just show as `None`.

**Email Address**: Pulls from Okta email address.

Generally, there isn't much to do here. Once a user is deactivated from Okta or deprovisioned from the Splunk app in Okta, access to Splunk is removed. However, the user list will still display the user without any status change. At some point in the future, users will need to be cleaned out of this list. For now, track the list of users that need to be removed in the [Systems Access Audit](https://docs.google.com/a/westfield.com/spreadsheets/d/1GGN0d8D2vJlg17qP-zEVvWvfcBEfOjmn40QTnVFrhSU/edit?usp=sharing) Google Sheet, in the `Splunk` worksheet tab.

## Amazon Web Services

AWS sits behind Okta but is also only accessed by a limited group of people, mostly pertaining to those on the Ops team. Still, AWS is included in the monthly audit as a precaution.

Access to AWS is only allowed from the [Okta User Home](https://westfieldlabs.okta.com/app/UserHome) page. You **must** be assigned the AWS app in order to see the app. When you click the app icon, it will take you to the main AWS Console for the Westfield Labs account. From there:

1. Look under `Security & Identity` and click on `Identity & Access Management`
2. From the menu bar on the left, click on `Users` to load a table of IAM Users
3. Click the `Password Last Used` column heading, twice, to sort with most recently used at the top
4. Look for users that are no longer active and have a check mark in the `Password` column, indicating that a password is required for login but has been **inactive** for some time. If the username uses a retailer/vendor name, check in with Peter McInerney or Alex Lee to see if we still collaborate with those vendors/retailers before removing.
5. If a user needs to be removed, click the checkbox to the left of the username and click `User Actions` towards the top to open a drop down menu
6. Select `Delete User` and confirm the deletion

