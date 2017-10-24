## Certified Jenkins Engineer
### Introduction to Jenkins
What is Jenkins?
* Leading open source automation server
* Lots of plugin support

Who uses it?
* Developers
* Sysadmins

What's so great about it?
* Open source
* Works for most people
* Easy to use and portable

### CI/CD Concepts
#### Continues Integration and Continuous Delivery
What is Continuous Integration?
* Multiple daily integrations to a mainline
* Automated testing

Keys and assumptions:
* People often wonder how they lived without it
* Assumes a high degree of testing (unit testing/integration testing)

Basic Workflow:
* Checkout from SCM(git)
* Branch and make changes
* Add or change tests
* Trigger automated build locally
* If successful, consider committing
* Update with latest from mainline


Best Practices

* Maintain a single repo and mainline branch
* Automate the build
* Minimize user error by using automation
* Make the build self-testing
* Everyone commits frequently
* Communication is key
* Merge frequently
* Build every commit and prioritize fixing broken  builds
* Keep your builds fast
* Testing environment should be as close as possible to prod
* Keep the Jenkins server open
  * Everyone should be contributing
* Automate the deployment process

What is continuous delivery?

* Development discipline where software is built so that it can be released to prod at any point
* Software is always deployable
* Not breaking the build is prioritized over adding features
* Feedback loop is short
* Push-button deployments are possible

Differences between CI and CD

* CD is the ability to release at any time
  * Means the code *CAN* be released at any time
* CI is the practice of integrating code continuously
* Continuous deployment
  * Means that code IS released continuously
