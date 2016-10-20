
## AEM Deployment Failures

AEM deployments that the Web Squad do are different from your usual Heroku deployments and therefore have a different troubleshooting process. This is because AEM was built on a different stack and is hosted "in-house" on our own Amazon Web Services instances.

## The Basics

[Prod Ops AEM Wiki](https://github.com/westfield/prodops/wiki/AEM)

## Testing


[AEM Deploy Specs](https://github.com/westfield/aem_deploy/blob/master/spec/acceptance/publisher_tests/url_check_spec.rb)

After a new code package has been uploaded and installed, but before any publisher instance is placed back in the ELB (Elastic Load Balancer), we run a test suite against that specific publisher instance to verify pages are loading after the deployment. This test suite simply makes a bunch of GET requests to a bunch of high profile pages on the site with lots of traffic. These are mainly the landing pages for all the centres in all the markets, as well as some pages at a deeper level (categories/products/stores/etc.).

The test suite checks for:

* Response code == `200`
* Page size >= `100 KB`
* Closing `</body>` and `</html>` tags
* Existence of JSP recompile errors
* Existence of HTML errors

If any page experiences any of these errors, the test suite fails that publisher instance and does not proceed with placing the instance back in the ELB and stops the deployment. This leaves the West/East region with only 3 publisher instances instead of 4, but with different code packages depending on where the deployment stopped. This means that end-users may have different browsing experiences based on which publisher they’re directed to by the ELB.

## Test Failures

1. **Response code**

	If the response code is anything other than a 200, the test suite will fail. This means that the Jenkins build server was unable to get a proper response to the GET request to the page. It’s best to triage to a Production Engineer for investigation.

2. **Page Size, Closing tags, JSP errors, HTML errors**

	JavaServer Pages (JSP) can have issues recompiling sometimes after deployments, as some pages may take some time to complete the recompilation process.

    1. **Troubleshooting steps:**

        1. Visually, it can look like a component on the page didn’t complete rendering, or errored before it could complete, causing the remainder of the page to stop loading.

        2. If the page looks fine, view the Page Source and scroll all the way down to the bottom of the source. Check for an error message, which usually will mention something referencing:

			* Cannot find a file
			* Cannot resolve to a type (`new-tile.tag`)
			* `category-tile.tag`

		If the situation matches anything in #1 or #2, it means there’s a JSP recompilation error. You’ll need to manually recompile JSPs and then kick off the deployment again. Otherwise, just re-run the deployment job.

    2. **Steps to recompile JSPs**

		The JSP recompile function doesn’t actually do what you would expect it to. Clicking this button actually flushes the compiled JSP files, and then utilizes JIT (just-in-time) recompilation to recompile a page on the first time it’s visited. Some pages take longer to recompile due to content, so this can pose an issue at times to our testing suite due to the GET requests timing out
To "recompile":

        1. Connect to vpn.wflops.net using your FreeIPA credentials. You must have an account set up for you in order to access the VPN. The VPN allows you to hit each publisher directly.

        2. From the Jenkins console output of the failed deploy, the test suite will print out the URLs for whatever page failed the test. What we’re looking for here is the hostname, which usually looks like:

				http://publisher02.production.us-east-1.aws.wflops.net:4503/content/scentre-group/au/www/bondijunction/en/events.html

			Grab the hostname and copy/paste that into your browser, then tack on `/system/console/slingjsp`.
			
			The resulting URL looks like:
			
				http://publisher02.production.us-east-1.aws.wflops.net:4503/system/console/slingjsp

			**Note:** The domain portion will change based on which publisher instance the test suite fails on. Reminder, there are 8 publisher instances in total, 4 in the West region and 4 in the East region.

        3. Click the button labeled `Recompile all JSPs`.

		Now that we’ve flushed JSPs, we need to kick in the JIT recompile by running the test suite locally (requires some command line knowledge):

		**Note:** If you’ve already got a clone of the repo, skip steps 1 and 2. Instead, after step 3, make sure to git pull the latest code down from the Westfield repo.

		1. Go to [https://github.com/westfield/aem_deploy](https://github.com/westfield/aem_deploy) and fork the repo to your own account.

		2. Clone your new fork of the repo ([https://help.github.com/articles/cloning-a-repository/](https://help.github.com/articles/cloning-a-repository/))

		3. From your terminal, change directory to where you cloned the repo into.

		4. From the folder root, run the acceptance test rake task. The environments (production/uat, publisher instance, east/west, etc.) vary of course, based on which publisher you’re troubleshooting:
			
				rake spec:acceptance:production publisher=publisher02.production.us-east-1.aws.wflops.net:4503

		5. If the tests pass, go ahead and kick off the deploy again by manually triggering via Jenkins (test.westfield.io/job/aem_<repo name>-deploy-<environment>)

## I don’t see my changes

The first step is to ask the developer to validate whether or not they can see their code changes if they hit the publisher instances directly. Hitting the publisher instance directly bypasses the Fastly CDN layer and doesn’t serve up cached content, which means that if the changes are visible while hitting the publisher directly, then we’ve got a caching issue:

### Caching issues
There’s a few places where caching can be an issue for the deploy:

* **Dispatcher caching**

	SSH into dispatcher then locate the file in the cache and remove it.

* **Fastly caching**

	A file can sometimes be cached on the Fastly side, requiring a cache purge in order to see changes. To purge the cache, get the URL of the specific file from the AEM engineer that made the change. Then, from your terminal:

		curl -X PURGE <url here>

* **New Javascript file not being retrieved**

	Sometimes the minified JS file can be cached at the Fastly layer, preventing newly deployed JS from being served to the end user.

If the issue is determined not to be a caching issue, check to see whether the content packages were uploaded and installed successfully:

### Package not installed

This one can be tricky to notice, as it’s not exactly immediately apparent if you don’t know to look for it. In the Jenkins deploy console output, look for a line that states something like:

	Uploading centre-content-0.2.1349.zip and installing to Publisher: publisher01-west.wflops.net

At this point, the script is doing exactly what is being described in that line. It’s uploading and installing the package. The next line is what’s important, as the next line determines whether or not the package was actually successfully uploaded and installed on the publisher:

	{"success":true,"msg":"Package installed"}

If this line doesn’t appear, then there’s a network failure somewhere between the Jenkins build slave and the publisher instance. A Production Engineer will have to take a look as this requires AWS access. You may attempt to redeploy, but if it fails again, it might be prudent to finish up the deploy manually to maintain parity between publishers. Instructions for manually deploying to UAT can be found here: [AEM Manual Depoloy UAT](https://github.com/westfield/prodops/wiki/AEM-Deploy-outline-for-UAT-environment)

Note: There’s also one rare occasion where we cycled out AEM admin credentials, which are configured as ENV vars in Jenkins. If the admin credentials were changed recently, check to make sure that the ENV vars are updated for each of the AEM jobs in Jenkins.

### Issues evicting a publisher instance or waiting for publisher to be healthy

There are rare instances where the build server encounters an issue while attempting to evict or reinstate each publisher instance from the ELB. You can view the script here:

[https://github.com/westfield/aem_deploy/blob/master/scripts/classes/load_balancer.rb](https://github.com/westfield/aem_deploy/blob/master/scripts/classes/load_balancer.rb)

If there’s an issue here, triage to a Production Engineer for investigation. The process will involve logging on to AWS console and checking to make sure the ELB and publisher instances are truly failing/unhealthy.
