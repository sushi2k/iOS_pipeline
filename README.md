# iOS_pipeline

This repo summaries best practices and tools that should be considered for an iOS Pipeline including security checks.

You need to have a mature CI/CD pipeline

Hardware need to be purchased on-premise, maybe can also deploy in AWS (might only work for enterprise)

## Management of iOS Development Certificates to sign apps

Who should own the enterprise certificates to sign apps?

How can the enterprise certificate be stored securely? 

Secure solution:
- Yubikey (Hardware)
- Vault

Insecure
- Private GitHub repo

### Ressources

- <https://developer.apple.com/support/certificates/>


## Security Tools

### Source Code Scanning

Not a good coverage in general cannot keep up to date with the latest version.

Coverity - https://scan.coverity.com/travis_ci
Fortify
Veracode

What could be covered in grep statements?


Would it makes sense to make rules in FindSecBugs?
Objective-C:
Swift:

Sonarqube https://docs.sonarqube.org/display/PLUG/SonarCFamily+for+Objective-C

### DAST

OWASP ZAP

### App Transport Security (ATS)

-> Report MobSF
-> Create your own shell script to detect insecure configuration in Info.Plist

### SSL Pinning

Testing via User Interface Testing 
https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/09-ui_testing.html

Executing a script with XCTest in the simulator and proxy through OWASP ZAP to see if successfull or not. 

### 3rd party libraries (SCA/dependencies)

Cocoapods/Carthage
https://snyk.io/docs/

JFrog Artifactory supports CocoPods
Blackduck mostly likely supports CocoPods
Sonatype Nexus Lifecycle - CocoaPods



### Automated Testing 

MobSF?
Needle?

### Credential and Secret Management

Vault
https://support.cloudbees.com/hc/en-us/articles/203802500-Injecting-Secrets-into-Jenkins-Build-Jobs

### Secret Scanning

https://dzone.com/articles/cloud-security-best-practices-finding-securing-and

#### Git Secrets

Released by AWS Labs, as you can guess by the name – it scans for the secrets. Git Secrets would be helpful to prevent committing AWS keys by adding a pattern.

```
$ git secrets --scan-history
149dc6a02b6bf2ab98992504d705ab9f2e6a6e0a:MSTG-JWT/ViewController.swift:19:    let aws_secret_access_key = /LLeaSVtLMc3L78X+BrbtMCg2uyDpw4Y+rGgYxOV
bf2fcf3c28168c716c2b86a653c6637fbaf87ca0:MSTG-JWT/ViewController.swift:19:    let aws_secret_access_key = /LLeaSVtLMc3L78X+BrbtMCg2uyDpw4Y+rGgYxOV
bf2fcf3c28168c716c2b86a653c6637fbaf87ca0:credentials:3:aws_secret_access_key = /LLeaSVtLMc3L78X+BrbtMCg2uyDpw4Y+rGgYxOV
065fc806523353e8353865437e83e3904a0627e6:credentials:3:aws_secret_access_key = /LLeaSVtLMc3L78X+BrbtMCg2uyDpw4Y+rGgYxOV
```

Works well for AWS Access Keys.

Reset git secrets:
```bash
# Remove varialbes:
$ git config --global --unset-all secrets.patterns
$ git config --global --unset-all secrets.allowed
$ git secrets --register-aws --global
$ git secrets --add --global "[A-Z0-9]{20}" // get aws_access_key_id, this pattern is not included when executing the command before 
$ cd "/Users/sven/Training/MSTG-SourceCode/iOS/Source Code/MSTG-JWT"
$ git secrets --scan -r
MSTG-JWT/ViewController.swift:18:    let aws_account_id = BKIAJM24KY33SBSYZUDA
MSTG-JWT/ViewController.swift:19:    let aws_secret_access_key = /LLeaSVtLMc3L78X+BrbtMCg2uyDpw4Y+rGgYxOV

[ERROR] Matched one or more prohibited patterns

Possible mitigations:
- Mark false positives as allowed using: git config --add secrets.allowed ...
- Mark false positives as allowed by adding regular expressions to .gitallowed at repository's root directory
- List your configured patterns: git config --get-all secrets.patterns
- List your configured allowed patterns: git config --get-all secrets.allowed
- List your configured allowed patterns in .gitallowed at repository's root directory
- Use --no-verify if this is a one-time false positive
```

https://github.com/dxa4481/truffleHog/blob/48ffdd3beee3b600939136fccad59f50a2610909/scripts/searchOrg.py#L10
https://github.com/auth0/repo-supervisor


#### Recommendations when using secret scanner

For example to enhance git-secret Performance:
To enable git-secret to catch more potential secrets and to do it without causing too many false positives, start by making a list of services you might use along with their respective API key formats:

PagerDuty
- Authorization token: 20 character alphanumeric (upper & lower) + some symbols
- Service Key: 32 character hex
GitHub
- Personal access token: 40 char hex
Threat Stack
- API key: 64 alphanumeric
- Deploy key: 72 alphanumeric
Slack
- Token: 74+ “xoxp-{11 numeric}-{12-13 numeric}-{32 hex}”

A good starting point are the following regexes from Trufflehog https://github.com/dxa4481/truffleHogRegexes/blob/master/truffleHogRegexes/regexes.json 


## Build Tools

- [Fastlane](https://fastlane.tools/)
