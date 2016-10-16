## Prepwork:

* Specs kicks off the Deploy UAT jobs, so disable these jobs as necessary based on why this manual deploy needs to happen. Usually Centre/Bluegrass/GPP need to be disabled to prevent teams from accidentally merging and deploying. Bundles should normally be disabled.

    [AEM Centre UAT Deploy](https://test.westfield.io/job/aem_centre-deploy-uat/)

    [AEM Bluegrass UAT DEPLOY](https://test.westfield.io/job/aem_bluegrass-deploy-uat/)

    [AEM GPP UAT DEPLOY](https://test.westfield.io/job/aem_gpp-deploy-uat/)

    [AEM Bundles UAT DEPLOY](https://test.westfield.io/job/aem_bundles-deploy-uat/)

    After disabling, have the involved teams merge their PRs.

* Get Centre/Bluegrass/GPP content packages via Artifactory (Jenkins user login stored in Lastpass):

    aem-releases-local > westfieldlabs > centre-content > latest release version # > download the .zip file there

    aem-releases-local > westfieldlabs > bluegrass-content > latest release version # > download the .zip file there

    aem-releases-local > westfieldlabs > gpp-content > latest release version # > download the .zip file there


## Deploy Author: author01-west.uat.wflops.net
Only 1 Author, no need to remove from ELB.

1. If a Bundles deployment/installation is required, open up Jenkins UAT deploy job for aem_bundles and comment out the execute shell sections for the publisher instances. Run the job for the Author instance.
    
    Alternatively, you can go to https://author01-west.uat.wflops.net/system/console/bundles and login with the UAT Admin credentials located in LastPass:
      1. Click `Install/Update`
      2. Check the boxes for `Start Bundle` and `Refresh Packages`, then click `Choose File` to select the bundle files that you need to install. Note that if there's any dependencies, you should select the files in that order.
      3. Click `Install or Update` to start the install.
      4. Refresh the page and check to make sure the version numbers are correct. You can usually work with someone from Team CW to verify this.

2. When deploy of bundles is complete, go to CRX on author01 and upload and install whatever packages (Centre/Bluegrass/GPP) that were downloaded from Artifactory.

3. If a restart of the instance is required, SSH into author01 to restart:
  1. Connect to the Free IPA VPN under vpn.wflops.net. Talk to an Ops teammate if you need an account set up.
  2. `ssh <Your FreeIPA Username>@author01.uat.us-west-1.aws.wflops.net`
  3. If you need to check what commands your user is allowed to perform: `sudo -l`
  4. Stop AEM: `sudo -u aem /mnt/aem/crx-quickstart/bin/stop`
  5. Start AEM: `sudo -u aem /mnt/aem/crx-quickstart/bin/start`
  6. Check that the java process restarted and is up and running, verify new process uptime and params: `ps -ef | grep java`
  7. Change directory to the `crx-quickstart` folder: `cd /mnt/aem/crx-quickstart/`
  8. Tail logs to watch when AEM has finished restarting: `tail -f error.log`

4. Go to `/system/console/bundles` on Author and verify that all updated bundles have the correct version number and that the timestamp is the current date/time.

Done

## Deploy Publishers:
As the Publishers serve up the actual content that end-users see, we need to rotate each publisher instance out of the Load Balancer and update them one at a time. This is to ensure that we have enough publisher instances to handle the load of traffic hitting our sites, as well as to prevent users from seeing errors whilst deploys are in process.

1. Log in to AWS via the User Apps page in Okta, where the AWS app should have been assigned to you. Change the role there to `ops @ aem-uat` in order to access the AEM UAT stack.

2. Go to EC2 > Load Balancers and select the `AEM-UAT` Load Balancer. Click the "Instances" tab and remove DISPATCHER01 from the Load Balancer. Tail the logs to verify no activity is hitting UAT:
  1. SSH to the jumphost (remember to include the necessary SSH key)
  2. Type in the alias: `publisher01`
  3. Change to AEM user: `sudo su aem`
  4. Tail logs: `tail -f /mnt/aem/crx-quickstart/logs/access.log`

3. If a Bundles deployment/installation is required, open up Jenkins UAT deploy job for aem_bundles and comment out the execute shell sections for the Author and publisher02 instances. Run the job for the publisher01 instance.
    
    Alternatively, you can go to https://publisher01-west.uat.wflops.net/system/console/bundles and login with the UAT Admin credentials located in LastPass:
      1. Click `Install/Update`
      2. Check the boxes for `Start Bundle` and `Refresh Packages`, then click `Choose File` to select the bundle files that you need to install. Note that if there's any dependencies, you should select the files in that order.
      3. Click `Install or Update` to start the install.
      4. Refresh the page and check to make sure the version numbers are correct. You can usually work with someone from Team CW to verify this.

4. When deploy of bundles is complete, go to CRX on publisher01 and upload and install whatever packages (Centre/Bluegrass/GPP) that were downloaded from Artifactory.

5. If a JSP recompile is necessary, go to https://publisher01-west.uat.wflops.net/system/console/slingjsp and click `Recompile JSP`

6. If a restart of the instance is required, SSH into publisher01 to restart:
  1. Connect to the Free IPA VPN under vpn.wflops.net. Talk to an Ops teammate if you need an account set up.
  2. `ssh <Your FreeIPA Username>@publisher01.uat.us-west-1.aws.wflops.net`
  3. If you need to check what commands your user is allowed to perform: `sudo -l`
  4. Stop AEM: `sudo -u aem /mnt/aem/crx-quickstart/bin/stop`
  5. Start AEM: `sudo -u aem /mnt/aem/crx-quickstart/bin/start`
  6. Check that the java process restarted and is up and running, verify new process uptime and params: `ps -ef | grep java`
  7. Change directory to the `crx-quickstart` folder: `cd /mnt/aem/crx-quickstart/`
  8. Tail logs to watch when AEM has finished restarting: `tail -f error.log`

7. Run the acceptance tests found in aem_deploy against pub01:

    `rake spec:acceptance:uat publisher=publisher01.uat.us-west-1.aws.wflops.net:4503`

8. If the acceptance tests pass, go back to AWS and click `EDIT INSTANCES` and select `dispatcher01` to be placed back in the Load Balancer. Wait until the Status for the instances shows `dispatcher01` as `InService`.

10. Repeat for publisher02 starting at Step #2.

**When done, re-enable whatever UAT Deploy jobs were disabled at start**

[AEM Centre UAT Deploy](https://test.westfield.io/job/aem_centre-deploy-uat/)

[AEM Bluegrass UAT DEPLOY](https://test.westfield.io/job/aem_bluegrass-deploy-uat/)

[AEM GPP UAT DEPLOY](https://test.westfield.io/job/aem_gpp-deploy-uat/)

[AEM Bundles UAT DEPLOY](https://test.westfield.io/job/aem_bundles-deploy-uat/)