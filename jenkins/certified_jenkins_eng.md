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


### Installing and Configuring Jenkins

#### Prerequisites

* Install Java
* Setup alternatives
* Setup JAVA_HOME environment variable

#### Jenkins Install

* Add yum the repository and install the version covered in the CJE in the exam

  ```shell
  wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
  rpm --import http://pkg.jenkins.io/redhat-stable/jenkins.io.key
  yum install -y jenkins-2.19.4-1.1
  ```

* Disable the yum repository to avoid an accidental update

  ```shell
  yum-config-manager --disable jenkins
  ```

* Setup alternatives

  ```shell
   alternatives --install /usr/bin/java java /usr/java/latest/bin/java 200000
   alternatives --install /usr/bin/javac javac /usr/java/latest/bin/javac 200000
   alternatives --install /usr/bin/jar jar /usr/java/latest/bin/jar 200000
  ```

* Enable the service to start on boot

  ```shell
  systemctl enable jenkins
  systemctl start jenkins
  ```

* Go to the Web GUI, set the password, and follow the setup wizard


#### User Management and Security

* Need to understand Jenkins user database for certification
  * Should still be aware of other options (servlet container, LDAP, Unix user/group db)
* Lots of questions on matrix based security
  * Permissions based on contexts (credentials, agents/slaves, jobs, etc...)
    * Job and project interchangeable
    * Agent and slave interchangeable

#### Adding a Jenkins Slave

* Add slaves when the monothlic instance can no longer handle the load

* Create a jenkins user on the slave and copy over the public key from the master

  ```shell
   useradd -d /var/lib/jenkins jenkins
  ```

* Setup alternatives and install java

  ```shell
  alternatives --install /usr/bin/java java /usr/java/latest/bin/java 200000
  alternatives --install /usr/bin/javac javac /usr/java/latest/bin/javac 200000 
  alternatives --install /usr/bin/jar jar /usr/java/latest/bin/jar 200000
  ```

* Check nodes view to see the status once the slave is launched

#### Plugin manager

* Plugins can be installed from
  * GUI
  * jenkins-cli
  * Upload file

* Option to restart isn't available for plugin uninstall
  * Need to select "prepare for shutdown" in "manage jenkins"
  * Restart from the jenkins service on the host

     ```shell
     systemctl restart jenkins
     ```

* Check the plugins wiki for archive versions

* Can install from file using the "Advanced Tab" in Plugin Manager

### Projects

#### Freestyle Project Configuration

* Organize slaves using labels
  * Can be used to organize by OS and distro
* Can configure a job to only run on specific nodes with pattern matching
  * ie. Linux && CentOS

#### Parameterized Projects

* Password parameters are not masked in console output

#### Upstream/Downstream Projects