# New hire setup

These steps need to be done before a user starts. It takes nearly a full week to complete this process.

## Badge

New hires should have a badge created for them the Friday before they start, so it can be ready for them on their first day. Badges take 2-3 days for Security to program, and there are process problems that can delay badge creation. The request needs to be made 3-4 days in advance.

**For contractors, the hiring manager must send a signed contract to John Perri, including the contract end date.**

1. Request Chantal or Ariel create the badge.

## Email

Email takes nearly a day to work fully after the account it created. The longest delay is the ability to receive email from external senders.

You will need to get the new hire's email (AD) password to finish the setup.

### FTEs

1. Submit IT request form
2. Workday will sync with AD to create the account
3. Follow up with local IT, contact them on #it-support or in person

### Contractors

1. Local IT in SF can create the accounts, contact them on #it-support or in person


## Computer

FTEs should get a new computer. Contractors get used computers, Matt McLean has a stash of laptops in his office.

Ideally new hires should be asked what computer they want, with strong suggestions. Engineers are strongly encouraged to get highly spec'd 15" rMBPs.

### Password change

So here's the deal. Okta is just-in-time provisioned. Yet we need to add users to Okta groups before they start. So we change their AD password, so we can log-in to Okta as them, so their account gets created.

1. Log-in to [mail.us.westfield.com](https://mail.us.westfield.com/)
2. Change the password (this should not be guessable, but you will need to type this a lot, so make it easy to remember and type)
3. Write this down, you will use it a lot, and give it to the new hire

### Install

1. Cmd-R on boot to boot into recovery partition
2. Use disk utilities to erase main partition
3. Use installer to install fresh OS
4. Create account in user’s name, using their temp AD password set above
5. Upgrade to the latest macOS, install additional OS updates, you will need to log-in to the App Store with an Apple ID
6. Install Xcode command line tools `xcode-select --install` 
7. Log out of App Store
8. In Security & Privacy preferences:
  1. Require password immediately after sleep or screen saver begins
  2. Enable FileVault, save file vault recovery key to file, tell new hire about file so they can put it in a password safe
9. In Energy Saver preferences, under Power Adapter:
  1. Set Turn display off after 5 minutes
  2. Prevent computer from sleeping automatically when the display is off
10. In Safari open a Private Window and download and install Chrome
11. In Chrome go to Chrome→About Google Chrome→Set Up Automatic Updates for All Users
12. In a Chrome Incognito Window download and install:
  - VirtualBox
  - Vagrant
  - Homebrew
  - Docker for Mac
13. Install MS Office (for Outlook) and Cisco VPN client, get the installers from IT
14. Log-in to Outlook
  - User name = `WESTFIELD\<username before @>`
  - It should detect the server correctly


## Okta
1. Log-in to Okta and Okta Preview as the new user to import their accounts
2. As an Okta admin add the new user to the following groups:
  - engineering (for Engineers)
  - Atlassian
3. Production Engineers should also be added to:
  - operations
  - app_dashboard.admin
  - splunk-ops
  - Plus they should be made Okta admins


## Invite to apps

### Google

Add to Google Apps for Work using new Westfield email address.

### Jenkins

1. Add to Jenkins (Manage Jenkins→Configure Global Security https://test.westfield.io/configureSecurity/) using the same email address as configured in Okta
2. Grant permissions based on what other similar users have

### Heroku

1. Create an account for the user at https://signup.heroku.com/
2. Access their email to confirm the account

The Heroku account should be created before Create attempts to grant permissions.

### JIRA

Add them to the projects for the team they will be on.

### LastPass

Invite user to LastPass.