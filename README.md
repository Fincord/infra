## Fincord - Create and maintain an extensible Discord based financial community, with inline bots for various puropuses and a scalable backend.
# infra
Fincord infrastructure CICD and glue-code

Data flow: 
- Backend daemons connect to data providers using apikeys, and store the gathered information in the cache layer
- Backend parsers digest the information and store some of it to be used by cache or persist premanently to various persistence endpoints
- A user / bot authenticates to Frontend and recieves an ephemeral JWT token
- Frontend makes a request (if verified token) on behalf of a user / bot to backend

Guidelines:
* Formats:
  + Date/time: 
    - Any and all date/time references must comply to the standart (https://tools.ietf.org/html/rfc3389), specifically the nano format.
    - All dates the you Create Retrieve Update or Delete should and will be UTC dates, that's GMT + 0000 (Zulu).
Since we have devs from all arround with diferrent time-zones, and we pull data from various sources, that's are only option to keep sanity.

  + Input/Output:
    - All the data that is sent or recieved by any part of the system should be JSON (https://tools.ietf.org/html/rfc7159),
      As a developer you shouldn't need to guess the format and therefor do not even return JSON arrays, wrap them with an object and document its structure.
      Examples:
      '{"error": 404, "reason": "Not Found"}'
      '{"results": [{"id": "111", "name": "Doc 1"}, {"id": "222", "name": "Doc 2"}]}'

* Code: (because we all know how hard it is to sort a project once it turned out to be messy)
  - PRs to dev branch, @ end of iteration/cycle/sprint and after being tested by @qa-gang merge to master and then CICD auto-releases.
  - Commit names must be clear and concise and yet not too long - Commits such as "Multiple Bugfixes" will not be merged
  - Undocumented code will not be merged
If we don't maintain those rules, it will be very hard for others to join us, and we don't want that.

* License: GPLv3
  All external libraries should be opensource, up to GPLv3, that means MIT/ Apache /... is fine but don't use something that will exposes us to lawsuits, I will make sure we strictly follow this rule.

* Glossary:
 - Platforms: Trading platforms (IBKR, TD ToS, Tradestation, MT4, ...) (TBD @backend-gang)
 - Data providers - anything that provides us external data, could be any market data from platforms, scrapers, spiders, scanners, crawlers, parsers, ...
[1:00 AM]
Structure - The project consists of 3 repositories:
* Backend - Golang API backend, with CLI and CRUD REST API resources capabilities
  The backends purpose is to gather and store information from external providers, index and store it, and provide it to whomever authenticates properly via custom callbacks on the messagebus (Etcd, allows atomic updates and distributed locks)
  Resources:
  - apikeys to data providers
  - apikeys and secrets to bots and Discord servers
  - quotes
  - option chains
  - company valuations and financial data
* Frontend - NodeJS + templated (Express, Angular, React. TBD @frontend-gang), Shows indicators, information, scoreboard, implements D3 (https://d3js.org/) for showing graphs etc
  The frontends purpose is to graphically alow a user to modify the operability of the underlying project and/or consume data/output in a visual way.
* Infrastructure:
  The infra repo holds the glue-code for the project, such as Terraform/Cloudformation templates, helm-charts, dashboards configuration, ...
  - Source control: GitHub, issues managed at the relevant repository, additions from external contributers using a PR and min 2 reviewers
inplace voting system to determine issues priority

- CICD: Jenkins / Circleci / Github actions. (TBD @devops-gang)
  - Cache / Hot storage: distributed Etcd cluster
  - DB: Couch/PG (TBD @dba-gang)
  - Cold storage: S3 buckets
  - Logging (TBD @devops-gang)
  - Monitoring ELK / Graphana (TBD @devops-gang)
  - Cloud provider: AWS
  - Environment: K8s , all microservices are using latest Docker images from official providers, our image stored @ official docker hub
  -  Secrets management: K8s secrets / Vault cluster + Shamir sharing (TBD @secops-gang)
