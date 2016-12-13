Welcome to Westfield Labs!

## Passwords

Secure your passwords! You only need to remember three passwords:

- Your computer password
- Your Active Directory (AD) password, used for email and Okta
- Your password safe password

Those passwords should be long and unique. Do not reuse password between services, or that you have used elsewhere.

All other passwords should be randomly generated and stored in your password safe. The means you need an encrypted password safe, and that password safe probably needs to be synced to the "cloud".

We have LastPass Enterprise. So if you want to use LastPass you can download the [Mac App or browser extension](https://lastpass.com/misc_download2.php).

If you don't want to use LastPass you can use another safe like [1Password](https://1password.com/), [MacPass](http://mstarke.github.io/MacPass/), [pwSafe](https://pwsafe.info/mac), or [Password Gorilla](https://github.com/zdia/gorilla/wiki) (get from Homebrew).

1. Pick a password safe
2. Put passwords in it
3. Put FileVault recovery key in it (found in ~/Documents)
4. Generate random password for new sites

### First steps

1. Log-in to computer
2. Install password safe
3. Change computer password (System Preferences -> Users & Groups -> Change Password)
4. Put password in password safe
5. Log-in to email at [mail.us.westfield.com](https://mail.us.westfield.com/)
6. Change AD/email password, put in password safe

## 2FA

Use two-factor authentication (2FA) on all sites that support it.

1. Log-in to sites and enable 2FA
  - [Okta](https://westfieldlabs.okta.com/)
  - [Google](https://drive.google.com/)
  - [Heroku](https://dashboard.heroku.com/)
  - [LastPass](https://lastpass.com/)


## GitHub


1. [Enable 2FA](https://github.com/blog/1614-two-factor-authentication) on GitHub account
2. Get someone in Production Engineering to add you to Create
  - If you are in Production Engineering, add yourself to [Create](https://create.westfield.io/)
3. Accept GitHub organization invite
4. Change [email routing](https://github.com/settings/notifications) for GitHub organization
5. Create [personal access token](https://github.com/settings/tokens)
  1. In .bashrc or similar and set `export HOMEBREW_GITHUB_API_TOKEN='<access token>'` 
6. [Unwatch](https://github.com/watching) repos


## Install software

### Homebrew

Homebrew should already be installed, use it to install useful software.

```
brew tap homebrew/completions
brew install git wget mtr ag hub heroku awscli bash-completion docker-completion docker-compose-completion vagrant-completion
```

### App Store

1. Log-in with personal Apple ID
2. Install Slack, LastPass


### Other nice to have software

Most of these are available from Homebrew or [Homebrew Cask](https://caskroom.github.io/) (`brew tap caskroom/cask`)

- [Sublime Text](https://www.sublimetext.com/3)
- [Postman](https://www.getpostman.com/)
- [GPGTools](https://gpgtools.org/)
- [rvm](https://github.com/rvm/rvm) or [rbenv](https://github.com/rbenv/rbenv)
- [nvm](https://github.com/creationix/nvm) or [nodenv](https://github.com/nodenv/nodenv)

## Git setup

See [Git guide](./Git_Guide.md)

## Editor configuration

Your `$EDITOR` should be configured to:

- Add a new lines at the end of files (`vim` and `emacs` do this by default)
- Use spaces instead of tabs (unless you are writing Go)
- Lint within the editor

In Sublime Text set the following preferences:

```
{
    "ensure_newline_at_eof_on_save": true,
    "translate_tabs_to_spaces": true
}
```

These Sublime Text packages from [Package Control](https://packagecontrol.io/) are recommended:

- SublimeLinter
- SublimeLinter-ruby
- SublimeLinter-rubocop
- Varnish VCL

## JIRA

Add an avatar to your [JIRA profile](https://jira.westfieldlabs.com/secure/ViewProfile.jspa).

Find your team's [boards](https://jira.westfieldlabs.com/secure/ManageRapidViews.jspa).

## Useful links

- [Okta](https://westfieldlabs.okta.com/) - single sign-on
- [Okta Preview](https://westfieldlabs.oktapreview.com/) - test version of single sign-on used for UAT and Dev apps
- [JIRA](https://jira.westfieldlabs.com/) - issue tracker
- [Confluence](https://wiki.westfieldlabs.com/) - useless wiki
- [Outlook Web Access](https://mail.us.westfield.com/) - place to change your AD password
- [Heroku](https://dashboard.heroku.com/) - where we run apps
- [Jenkins](https://test.westfield.io/) (aka Test) - CI system ([list of all jobs](https://test.westfield.io/api/xml?tree=jobs[name]))
- [Create](https://create.westfield.io/) - create apps and manage permissions
- [Deliver](https://deliver.westfield.io/) - dashboard for deploying apps
- [New Relic](https://rpm.newrelic.com/accounts/10589/applications) - application performance and error monitoring
- [On-call](http://pagerduty-service.herokuapp.com/pagerduty) - see who is on-call
- [Splunk](https://westfieldlabs.splunkcloud.com/) - logging
- [Submit](https://submit.westfield.io/) - internal site for submitting issues to ProdEng
- [PagerDuty](https://westfieldlabs.pagerduty.com/) - wake up!

### Team-specific documentation

- [github.com/westfield/prodops](https://github.com/westfield/prodops)
- [github.com/westfield/engineering](https://github.com/westfield/engineering)
- [github.com/westfield/prodops/wiki](https://github.com/westfield/prodops/wiki)

