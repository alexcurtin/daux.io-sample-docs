# How to code review

## Writing a Pull Request

A Pull Request (PR) is a request for code review.

### Pick reviewers

Pick reviewers who have appropriate domain expertise and context to provide a valuable review, while also balancing who is available to do the review. Specifically assign them to the PR and also @ mentioning them (and because people don't read email, ping them on Slack too :smile:).

Any code related to the following must be reviewed by someone with sufficient security experience (member of the Security Review Team when we have one):

- Session handling
- Access control
- Password handling
- Cryptographic hashing
- Encryption, including TLS
- Process or serving of user uploads

Any new implementations of the above should go through a design review by the Security Review Team prior to implementation.


### Review Contents

The GitHub PR contents will be pulled from the commit message, and can be altered at the time the PR is created. When the PR is squashed and merged, the PR contents should be used for the new commit message.

#### Title

The code review request (PR) title should include the issue number in square brackets. The title should be short and descriptive, like and email subject. To make the title fit in all the places it appears, try to limit it to no more than 50 characters.

Consistency makes reading easier.  Start the title with a capitalized imperative verb, such as `Fix` , `Add` , `Update` , etc.

Example: `[ABC-123] Add octocat gifs form`

If you are entering the title in a git commit, it should be followed by one blank line, then the body.

#### Body

Try to limit the body (comment) of the PR to 72 characters. It should cover:


- Motivation for change
- How the change will impact existing behavior
- Wider scope of impact (other services, teams, customers)
- Test Plan!

Example:

```
Users need to be able to submit octocat gifs to us, because they are
awesome!

- Add form to submit octocat gifs
- Save the gifs to local disk.

We have added a large disk to the web server to store all these amazing
gifs.

We are not currently serving the gifs, a CDN will be added prior to
serving these amazing octocat gifs.
```

#### Test Plan

The review request body should include a Test Plan which details how you have tested or plan to test the change.

Example:

```
**Test Plan:**

Tested the new octocat form in local dev environment.
Tested with Chrome and Firefox.
Tested existing forms still work.
Checked DB table to ensure data is written correctly.
Will test with IE, Android, and iOS once deployed to staging.

Wrote Selenium test for new form. Existing Selenium tests still pass.
```

#### All together

When writing the code review request in a commit message, it will look like this in `vim`:

```
[ABC-123] Add octocat gifs form

Users need to be able to submit octocat gifs to us, because they are
awesome!

- Add form to submit octocat gifs
- Save the gifs to local disk.

We have added a large disk to the web server to store all these amazing
gifs.

We are not currently serving the gifs, a CDN will be added prior to
serving these amazing octocat gifs.

**Test Plan:**

Tested the new octocat form in local dev environment.
Tested with Chrome and Firefox.
Tested existing forms still work.
Checked DB table to ensure data is written correctly.
Will test with IE, Android, and iOS once deployed to staging.

Wrote Selenium test for new form. Existing Selenium tests still pass.

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch octocat-gifs
# Changes to be committed:
#>------modified:   upload.rb
#
```

## Reviewing code

### Ask questions

If you are unsure about any aspect of the change — ask about it. Ask for clarification on motivation, impact, test plan, edge cases — any questions that pop into your head.

If you still aren’t comfortable reviewing the code after asking questions, there is good chance that there is a more appropriate reviewer; someone with more context or expertise in the area. Communicate that to the requester.


### You are not a robot

Code reviews are not syntax checks (in most cases). It is expected that the code is runnable and works as intended for the basic use case. The Test Plan should communicate how the requester verified it works as intended.

Automatic linters and other static analysis should catch common style and security issues.


### What to look for

#### Tests
Tests that are triggered by PR creation must pass before merging.

#### Application security

##### User input

Sanitize user input. Unsanitized user input, either at the time it is inputted or the time it is outputted, is the number one cause of application vulnerabilities.

SQL Injections and remote shell execution can be caused by working on user input after it is entered and before or after it has been stored in a database. Applications must use their framework or languages escape methods on user input before executing commands that use any user input.

[Cross-site scripting](https://en.wikipedia.org/wiki/Cross-site_scripting) (XSS) can be caused by working with user input retrieved from a database or API, and displayed in a web interface. HTML and/or Javascript escaping are required when user input is displayed in a web interface.

User input includes all data that a user can determine the content of, not just form data. Examples including web pages fetched on behalf of the user, XML files uploaded, email addresses, images that will be processed, and much more.

Structured (serialized) data formats can have many advanced features that are rarely used, but can by used for remote execution or data exfiltration. Use “safe” loading method to load user supplied data structures.

Untrusted user provided uploads should never be served directly. For example if users can upload images, those images should be processed and the processed versions served. If users can serve arbitrary content they can use our servers for malware distribution.

##### Endpoint protection

HTTP endpoints need to be secured, for example a user should only be able to POST an account update to their own account. That is probably pretty obvious, but can get more complicated if an authenticated user didn’t intend to hit the endpoint.

[Cross-site request forgery](https://en.wikipedia.org/wiki/Cross-site_request_forgery) (CSRF) happens when when a user is tricked into hitting a URL they are already authenticated to (cookie stored). Many web frameworks have CSRF protection. Know your framework, the protections it provides, and the CORS policy of your site.

GET endpoints can be especially dangerous because of their ease of access, look out for GET endpoints that make changes. But all methods are susceptible for CSRF. Look for endpoints where the combination of method, CORS policy, cookies, CSRF token do not provide protection. Both data updates (POST) and data retrieval (GET) need to be protected.

#### Approach

Is this the correct approach to solve the problem? Hopefully this is discussed in a design review prior to implementation.

#### Completeness

Does the code completely solved the problem? Look for missing functionality or edge cases.

If there are API changes, ensure matching changes are made to Swagger if Swagger is used.

#### Complexity

Simple code and architectures are easier to understand, maintain, and debug. As a result they are often more reliable. Look out for code or architecture you would dread having to troubleshoot at 2am.

#### Readability

Did you struggle to figure out what the code was doing? Could it be written in a more readable way? Or maybe it just needs some comments?

#### Consistency

Ensure the code follows project, team, and company conventions. This includes stylistic conventions not caught by lint checks, consistent naming conventions within and across projects, and use of the same libraries or functions for the same purpose.

#### Compatibility

Do you foresee compatibility issues with existing code or upstream consumers?

#### Reliability

Are possible exceptions handled correctly? Are exceptions covered up so no one will know when something is failing? Will retry logic cause unnecessary load?

Look for:

- External calls without error handling
- Catching all exceptions, not a specific exception
- Unlimited or unthrottled retries that will increase load on already failing systems

#### Impact

Who and what will be impacted? Did the requester mention the impact? Do people need to be notified?

##### Systems impact

- Will it increase memory usage?
- Will is increase CPU usage?
- Can the above be reduced?
- Does a new system need to be setup before the code can be deployed?

##### User impact

- For an API change, do internal or external developers need to be notified?
- For a product change, are product and support people ready for the change to be released?

##### Performance impact

- Does the change remove the ability to cache something?
- Does it introduce a new external request?
- Are external requests or DB lookups done efficiently?
- Should there be additional caching of costly calculations or lookups?


### Accept or not

Write any relevant comments.

If the PR would be dangerous to merge, label it with `Do Not Merge` , and leave a comment.

If you are ready accept the PR write a comment containing the string `LGTM`.
