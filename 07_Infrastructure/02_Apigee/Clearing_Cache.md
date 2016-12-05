## Clearing Apigee Cache
### Clearing a single page:
To clear the cache for a single page, we'll need to build a curl command that relates to that page.

- First we'll need to base64 encode our credentials to use in the cURL command: 

        export CREDS=$(echo apigeeusername:apigeepassword | base64)

- Now we need to determine the `BASE_PATH` and `ENVIRONMENT` of the page's cache you're trying to clear.
- Login to [Apigee](https://login.apigee.com), from the `APIs` dropdown click `API Proxies`. 
- From this page select the proxy that your app would fall under.
- On this page, you should see a section titled `Deployments` and a section called `Proxy Endpoints`. 
- Copy the value of the key for `Environment` and `Base Path` from each section respectively. 
- Now set each of these values to a variable in your shell as shown below. For example if I needed to clear the cache from a stores api page I would run the following: 
  
        export BASE_PATH=v1
        export ENVIRONMENT=production
        
- Set a variable of the URI of the page to clear:

        export URI=/stores/exampleid=1

- Now We have enough information to run the curl command to purge the cache:

        curl -X POST --header "Content-Type: application/octet-stream" --header "Authorization: Basic $CREDS" "https://api.enterprise.apigee.com/v1/organizations/westfieldlabs/environments/$ENVRIONMENT/caches/advanced_cache/entries/$BASE_PATH$URI?action=clear"