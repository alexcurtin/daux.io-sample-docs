## Jenkins
Currently [test.westfield.io](https://test.westfield.io) points to our Jenkins instance.  The Jenkins server lives in AWS with approximately 6 build nodes that it relies upon.

### Database Migrations in a Ruby Rails Application
This is easly to accomplish because of written logic within the `run` task in the [CI](https://github.com/westfield/ci) repository in GitHub.  To automate database migrations during the various stages of the deployment pipeline, simply add a file named `.ci` into the root of your application's project repository in GitHub.  The contents of the file are empty, as the logic simply checks for the existence of a file with that name.
