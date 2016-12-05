# New Server Enrollment
## Overview
This guide will walk you through enrolling a freshly deployed Centos7 EC2 instance in both Spacewalk and FreeIPA. The DHCP options for all of our AWS regions in all accounts have nameservers pointing to FreeIPA which makes the enrollment process simpler. No hosts file hacks here.

### Don't get locked out
#### Update sshd config to permit root login
- Edit `/etc/ssh/sshd_config` and uncomment `PermitRootLogin Yes`
- Restart ssh: `service sshd restart`
- Edit `/root/.ssh/authorized_keys` and comment out:
  
        no-port-forwarding,no-agent-forwarding,no-X11-forwarding,command="echo 'Please login as the user \"centos\" rather than the user \"root\".';echo;sleep 10" 
   
- Make a new line with just the SSH key you're using to login
- Test logging in as root: `ssh -i /path/to/key root@ip-of-machine` If you can login you're ready to move on.

## Spacewalk
### Enroll
###### As root run the following commands
#### Install the yum repositories

    $ rpm -Uvh http://yum.spacewalkproject.org/2.5-client/RHEL/7/x86_64/spacewalk-client-repo-2.5-3.el7.noarch.rpm
    $ BASEARCH=$(uname -i)
    $ rpm -Uvh http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
      
#### Install the packages

    $ yum -y install rhn-client-tools rhn-check rhn-setup rhnsd m2crypto yum-rhn-plugin rhncfg-actions osad
    $ yum -y remove rhn-org-trusted-ssl-cert-1.0-1.noarch
    $ rpm -Uvh http://spacewalk.wflops.net/pub/rhn-org-trusted-ssl-cert-1.0-1.noarch.rpm    

#### Now that you have all of the software dependencies installed, go ahead and register the server.
###### Note: you will need an activation key for this. Make sure you have created activation keys, and imported the relevant software channels into spacewalk already.  

    $ rhnreg_ks --serverUrl=https://spacewalk.wflops.net/XMLRPC --sslCACert=/usr/share/rhn/RHN-ORG-TRUSTED-SSL-CERT --activationkey=1-centos-7 --force
  
#### Enable services and pull base configurations from spacewalk

    $ rhn-actions-control --enable-all
    $ rhncfg-client list
    $ rhncfg-client get
    $ service osad start
    $ chkconfig osad on
    $ rhn-profile-sync
    $ hostname

Login to https://spacewalk.wflops.net, find the host you just added (search with hostname from previous command) and edit the hostname to match that which you've set as the name in AWS, e.g. `mygreatserver.production.us-west-1.aws.wflops.net`.

#### Back on the server:

    $ rhncfg-client get
    $ rhn-profile-sync
    $ yum -y update
    $ reboot

## FreeIPA
### Enroll
###### As root:
#### Confirm that the correct nameservers are in your `/etc/resolve.conf`

    nameserver 10.254.194.163
    nameserver 10.254.195.78
  
#### Stop and remove NetworkManager if it is installed:

    $ service NetworkManager stop
    $ yum -y remove NetworkManager\*
  
#### Null any existing ldap configurations and install the ipa package:

    $ authconfig --update --disableldap --disableldapauth --ldapbasedn='' --ldapserver=''
    $ yum -y install ipa-client

#### Uninstall ipa-client if it is already installed

    $ ipa-client-install --uninstall --unattended
  
#### Configure NTP

    $ yum -y install ntp
    $ service ntpd stop
    $ ntpdate 17.253.4.253
    $ service ntpd start
    $ chkconfig ntpd on 
    
#### Install the ipa-client and follow the prompts on screen. Defaults are fine:

    $ ipa-client-install --no-ntp --mkhomedir -N --ssh-trust-dns --enable-dns-updates
  
## Cleanup
#### Update sshd config to disallow root login
- Edit `/etc/ssh/sshd_config` and comment out `PermitRootLogin Yes`
- Edit `/root/.ssh/authorized_keys` and uncomment:  
  
        no-port-forwarding,no-agent-forwarding,no-X11-forwarding,command="echo 'Please login as the user \"centos\" rather than the user \"root\".';echo;sleep 10" 
   
- Put the SSH key on the same line as the content above.
- Test logging in as root: `ssh -i /path/to/key root@ip-of-machine` If you can't login you're done!