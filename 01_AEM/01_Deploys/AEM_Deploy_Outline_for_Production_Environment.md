#AEM Deploy outline for Production environment.

## Prepwork:

* Make sure that the changes that were deployed to UAT are verified by the developers before beginning deployment to Production. This step is important because we should never deploy to production without having deployed to UAT first.

* If, for some reason, you don't have the packages needed for deployment from the previous UAT deploy, you can get the Centre/Bluegrass/GPP content packages via one of the UAT publishers:

		http://publisher01.uat.us-west-1.aws.wflops.net:4503/crx/de/index.jsp
		http://publisher02.uat.us-west-1.aws.wflops.net:4503/crx/de/index.jsp
	
	Log in to the publisher as Admin using the credentials found in Lastpass. Then go to the Package Manager page to view a list of all packages installed on that publisher. Find the package you need, then click on it to expand the menu. Click `Download` to initiate downloading of the package.

## Deploy Author:

	author01.production.us-west-1.aws.wflops.net

Only 1 Author, no need to remove from ELB.

1. If a Bundles deployment/installation is required, open up Jenkins Production deploy job for aem_bundles and comment out the execute shell sections for the publisher instances. Run the job for the Author instance.
    
    Alternatively, you can go to [https://author01-west.production.wflops.net/system/console/bundles](https://author01-west.production.wflops.net/system/console/bundles) and login with the Production Admin credentials located in LastPass:
      1. Click `Install/Update`
      2. Check the boxes for `Start Bundle` and `Refresh Packages`, then click `Choose File` to select the bundle files that you need to install. Note that if there's any dependencies, you should select the files in that order.
      3. Click `Install or Update` to start the install.
      4. Refresh the page and check to make sure the version numbers are correct. You can usually work with someone from Team CW to verify this.

2. When deploy of bundles is complete, go to CRX on author01 and upload and install whatever packages (Centre/Bluegrass/GPP) that were downloaded from Artifactory.

3. If a restart of the instance is required, SSH into author01 to restart:
  1. Connect to the Free IPA VPN under vpn.wflops.net. Talk to an Ops teammate if you need an account set up.
  2. `ssh <Your FreeIPA Username>@author01.production.us-west-1.aws.wflops.net`
  3. If you need to check what commands your user is allowed to perform: `sudo -l`
  4. Stop AEM: `sudo -u aem /mnt/aem/crx-quickstart/bin/stop`
  5. Start AEM: `sudo -u aem /mnt/aem/crx-quickstart/bin/start`
  6. Check that the java process restarted and is up and running, verify new process uptime and params: `ps -ef | grep java`
  7. Change directory to the `crx-quickstart` folder: `cd /mnt/aem/crx-quickstart/`
  8. Tail logs to watch when AEM has finished restarting: `tail -f error.log`

4. Go to `/system/console/bundles` on Author and verify that all updated bundles have the correct version number and that the timestamp is the current date/time.

Done

## Deploy Publishers:
	publisher01.production.us-west-1.aws.wflops.net
	publisher02.production.us-west-1.aws.wflops.net
	publisher03.production.us-west-1.aws.wflops.net
	publisher04.production.us-west-1.aws.wflops.net
	publisher01.production.us-east-1.aws.wflops.net
	publisher02.production.us-east-1.aws.wflops.net
	publisher03.production.us-east-1.aws.wflops.net
	publisher04.production.us-east-1.aws.wflops.net

As the Publishers serve up the actual content that end-users see, we need to rotate each publisher instance out of the Load Balancer and update them one at a time. This is to ensure that we have enough publisher instances to handle the load of traffic hitting our sites, as well as to prevent users from seeing errors whilst deploys are in process.

1. Log in to AWS via the User Apps page in Okta, where the AWS app should have been assigned to you. Change the role there to `ops @ aem-production` in order to access the AEM Production stack. Start with the US West (N. California) region.

2. Go to EC2 > Load Balancers and select the `AEM-Production` Load Balancer. Click the "Instances" tab and remove DISPATCHER01 from the Load Balancer. Tail the logs to verify no activity is hitting Production:
  1. SSH to the jumphost (remember to include the necessary SSH key)
  2. Type in the alias: `publisher01`
  3. Change to AEM user: `sudo su aem`
  4. Tail logs: `tail -f /mnt/aem/crx-quickstart/logs/access.log`

3. If a Bundles deployment/installation is required, open up Jenkins Production deploy job for aem_bundles and comment out the execute shell sections for the Author and publisher02 instances. Run the job for the publisher01 instance.
    
    Alternatively, you can go to https://publisher01-west.production.wflops.net/system/console/bundles and login with the Production Admin credentials located in LastPass:
      1. Click `Install/Update`
      2. Check the boxes for `Start Bundle` and `Refresh Packages`, then click `Choose File` to select the bundle files that you need to install. Note that if there's any dependencies, you should select the files in that order.
      3. Click `Install or Update` to start the install.
      4. Refresh the page and check to make sure the version numbers are correct. You can usually work with someone from Team CW to verify this.

4. When deploy of bundles is complete, go to CRX on publisher01 and upload and install whatever packages (Centre/Bluegrass/GPP) that were downloaded from Artifactory or grabbed from the UAT publisher.

5. If a JSP recompile is necessary, go to https://publisher01-west.production.wflops.net/system/console/slingjsp and click `Recompile JSP`

6. If a restart of the instance is required, SSH into publisher01 to restart:
  1. Connect to the Free IPA VPN under [vpn.wflops.net](vpn.wflops.net). Talk to an Ops teammate if you need an account set up.
  2. `ssh <Your FreeIPA Username>@publisher01.production.us-west-1.aws.wflops.net`
  3. If you need to check what commands your user is allowed to perform: `sudo -l`
  4. Stop AEM: `sudo -u aem /mnt/aem/crx-quickstart/bin/stop`
  5. Start AEM: `sudo -u aem /mnt/aem/crx-quickstart/bin/start`
  6. Check that the java process restarted and is up and running, verify new process uptime and params: `ps -ef | grep java`
  7. Change directory to the `crx-quickstart` folder: `cd /mnt/aem/crx-quickstart/`
  8. Tail logs to watch when AEM has finished restarting: `tail -f error.log`

7. Run the acceptance tests found in aem_deploy against pub01:

    `rake spec:acceptance:production publisher=publisher01.production.us-west-1.aws.wflops.net:4503`

8. If the acceptance tests pass, go back to AWS and click `EDIT INSTANCES` and select `dispatcher01` to be placed back in the Load Balancer. Wait until the Status for the instances shows `dispatcher01` as `InService`.

10. Repeat for publisher02-04 starting at Step #2. When all 4 publishers are deployed to in the West, change region to US East (N. Virginia) to go through all 4 publishers there.
