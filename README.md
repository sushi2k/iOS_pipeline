This is mainly a brain dump from @commjoen. Thanks for sharing your insights during the Open Security Summit 2019!

# iOS_pipeline

This repo summaries best practices and tools that should be considered for an iOS Pipeline including security checks.

Before introducing security tools, make sure that you have a mature CI/CD pipeline:
- Build process is stable
- Tools like Terraform are used to build a consistent and ideally immutable environment
- Containerize as much as possible and use their APIs
- Supression happens through a centralised vulnerablity manager (Threatfix)
- Secure your pipeline components (physical and digital)

macOS is not available as a well maintained and scalable image for all big cloud providers. Either you go for a specialized cloud provider or you need to purchase hardware on-premise to setup a iOS pipeline.

## Management of iOS Development Certificates to sign apps

How can the enterprise certificate be stored securely?

Secure solutions:
- Hardware keys (USB) that are plugged into the build server when creating new releases
- Vault

Approach with fastlane: https://docs.fastlane.tools/actions/pem/

### Ressources

- <https://developer.apple.com/support/certificates/>

## Security Tools

### Source Code Scanning

Not a good coverage in general for mobile technology stacks and cannot keep up to date with the latest version. Stick to a version in your programming language that is supported by the tool you are using, the latest and great will usually not be supported in your SAST tool or apply another strategy.

Tools might be:
- Coverity
- Fortify
- Veracode

Instead you can use your own grep statements. (You need to identify the keywords that are applicable for your project and maintain it).

Would it makes sense to make your own rules in FindSecBugs?
Sonarqube https://docs.sonarqube.org/display/PLUG/SonarCFamily+for+Objective-C

Xcode CLANG

Leakcanary https://github.com/haifengkao/LeakCanaryiOS
Check for memory leaks in the pipeline

What about Xamarin?
What about JavaScript (retire.js)?

### DAST

OWASP ZAP

### App Transport Security (ATS)

-> Report MobSF
-> Create your own shell script to detect insecure configuration of ATS in Info.Plist

### SSL Pinning

Testing via User Interface Testing 
https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/09-ui_testing.html

Executing a script with XCTest in the simulator and proxy through OWASP ZAP to see if successful or not.
Ideally use a real device!

### 3rd party libraries (SCA/dependencies)

Cocoapods:
- JFrog Artifactory
- Blackduck mostly likely supports CocoPods
- Sonatype Nexus Lifecycle - CocoaPods
https://jeremylong.github.io/DependencyCheck/analyzers/cocoapods.html

Nothing at the moment in Carthage?

C/C++ Libraries - Check with SAST tools the libraries

### Automated Testing

- MobSF
- Needle (need jailbroken device)
- objection/frida (repackage app or jb device)
- XCTesting

## Build Tools

- [Fastlane](https://fastlane.tools/)
