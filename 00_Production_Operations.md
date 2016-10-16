## Purpose
The Production Operations (ProdOps) team is responsible for the creation, support, triage, and remediation of production infrastructure and its dependent platforms. As a supplement to the upkeep of that production envrionment, this team is also tasked with oversight and instrumentation of all code deployment pipelines.  The goal for the ProdOps team is to empower the various teams within the core business to perform their jobs in a timely and responsible manner with repetition and scale. 



## Core Aspects and Responsibilities
### Platforms
ProdOps are administrators or superusers for the majority of platforms that are in use.  With this responsibility comes some basic maintenance and upkeep tasks to support and utilize the platforms as the team best determines in conjunction with the core users and stakeholders of these SaaS and PaaS systems.

The majority of these platforms are being utilized for metrics, logging, monitoring, and reporting of various infrastucture across our global footprint. 


### Process
The maintainenance of documentation for key processes and infrastucture in the core business are owned by ProdOps.  Additional steps, workflows, runbooks that are critical to this team functioning should be found within this repository in various relevant sections, such as the [wiki](https://github.com/westfield/prodops/wiki).  This includes determining workflows for code deployments for various projects as well as PCI documentation.


### Systems
ProdOps owns and has access to the physical (or virtual) servers that host our applications.  When new projects arise, it is up to this team to work with project leads and engineers to determine the infrastructure needs as well as establishing a runbbook for procedural support of the infrastructure in question.


### Development
To assist the engeering team as well as the ProdOps team internally, a lot of work goes into the development and refactoring of proprietary tooling.  This includes the [**Create**](https://create.westfield.io/), [**Test**](https://test.westfield.io/), [**Deliver**](https://deliver.westfield.io) suite of tools and other microservices that exist on the various [Systems](#systems) and [Platforms](#platforms). If not creating or working on something new, the team is tasked with code maintenance and support for these various applications (including API integrations for third-party reliant systems).

### Performance
A new function of the ProdOps team is to assist with production site performance.  Given the monitoring and analytics tool at the team's disposal, ProdOps can help determine problematic areas on various sites that Westfield Labs maintains, as well as propose the remediation steps to fall within the Westfield Labs SLA and site uptime.



### On-Call Schedule and Rotation
ProdOps is reponsible for managing any escalated support inquiries and tasks.  Once the Business Support team takes the first pass at a support inquiry, and they determine the request is out of the scope of their business function, it can be escalated to this team and either be determined to be project work or an immediate support request.

For any major issues, members of the team can be notified via the [#support](https://westfieldlabs.slack.com/messages/support/) Slack channel.<br>

The business and after hours on-call schedule can be located [here](http://pagerduty-service.herokuapp.com/pagerduty).


## Team Members
Name | Details
---- | -------
Peter McInerney | VP of Production Engineering<br><pmcinerney@us.westfield.com>
Alex Lee | Production Engineer<br><alee@us.westfield.com>
Jeremy Shapiro | Production Engineer<br><jshapiro@us.westfield.com>
Alex Curtin | Production Engineer<br><acurtin@us.westfield.com>
Anton Cohen | Production Engineer (c)<br><nwe.acohen@us.westfield.com>
Jay Gabriels | Production Engineer<br><jgabriels@us.westfield.com>
Alex Pinchuk | Production Engineer<br><nwe.apinchuk@us.westfield.com>
Brian Fong | Performance Engineer (c)<br><nwe.bfong@us.westfield.com>