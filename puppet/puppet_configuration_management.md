#### Describe language features

[Language: Basics](https://docs.puppetlabs.com/puppet/latest/reference/lang_summary.html)

* Syntax inspired by the Nagios configuration file format

* Resources, classes, and nodes

    * core of the language is declaring resources

    * groups of resources are organized into classes

    * scope

        * resource describes a file or package

        * class describes a service or application

        * larger classes describe entire system

* Ordering

    * variables must be declared before referenced

* Files

    * called manifests

    * .pp extension

    * must use UTF8 encoding

    * may use Unix (LF) or Windows (CRLF) line breaks

* Compilation and catalogs

    * manifests can use conditional logic

    * catalogs are static documents which contain resources and relationships

        * will be in memory as a Ruby object, transmitted as JSON, and persisted to disk as YAML

    * master server compiles catalogue for agent

    * agent nodes cache their most recent catalog

[Language: Node Definition](https://docs.puppet.com/puppet/latest/reference/lang_node_definitions.html)

* Matching

    * If there is a node definition with the node’s exact name, Puppet will use it.

    * If there is a regular expression node statement that matches the node’s name, Puppet will use it. (If more than one regex node matches, Puppet will use one of them, with no guarantee as to which.)

    * If the node’s name looks like a fully qualified domain name (i.e. multiple period-separated groups of letters, numbers, underscores and dashes), Puppet will chop off the final group and start again at step 1. (That is, if a definition for www01.example.com isn’t found, Puppet will look for a definition matching www01.example.)

    * Puppet will use the default node.

* Example

    * Thus, for the node www01.example.com, Puppet would try the following, in order:

        * www01.example.com

        * A regex that matches www01.example.com

        * www01.example

        * A regex that matches www01.example

        * www01

        * A regex that matches www01

        * default

[Language: Reserved Words and Acceptable Names](https://docs.puppet.com/puppet/4.6/reference/lang_reserved.html)

* Reserved words

    * cannot be used as bare word strings

        * you must quote these words if you wish to use them as strings

    * cannot be used as names for custom fucntions

    * cannot be used as names for classes

    * cannot be used as names for custom resources types or defined resource types

    * reserved words list

        * and — expression operator

        * case — language keyword

        * class — language keyword

        * default — language keyword

        * define — language keyword

        * else — language keyword

        * elsif — language keyword

        * false — boolean value

        * if — language keyword

        * in — expression operator

        * import — language keyword

        * inherits — language keyword

        * node — language keyword

        * or — expression operator

        * true — boolean value

        * undef — special value

        * unless — language keyword

        * Future use

            * attr

            * function

            * type

            * private

* Reserved class names

    * main

        * Puppet automatically creates a **main **class, which contains any resources not contained by any other class

    * settings

        * automatically created namespace that contains variables with the settings available to the compiler

            * puppet master settings

* Reserved variable names

    * **must not assign values to:**

        * $string

        * any variable consisting only of numbers (ie. $0)

        * $trusted

        * $facts

* Variables, Classes, Types, Tags, Params and modules

    * Begin with **$**

    * Can include

        * uppercase and lowercase letters

        * numbers

        * underscores

* Classes and Types

    * must begin with lowercase letter

    * can contain

        * lowercase letters

        * numbers

        * underscores

[Language: Resources](https://docs.puppet.com/puppet/4.6/reference/lang_resources.html)

* Syntax

    * resource type

    * an opening curly brace

    * the title, which is a string

    * a colon

    * optionally, any number of attribute and value pairs

        * a => arrow

    * a trailing coma

    * a closing curly brace

    * type {'title':      attribute => value,    }

* Type

    * identifies what kind of resource it is

* Title

    * identifying string

    * must be unique per resource type

* Attributes

    * describe the desired state of the resource

    * each resource has its own set of available attributes

* Behavior

    * adds a resource to the catalog

    * tells Puppet to manage that resource’s state

    * when the catalog is compiled, it will

        * read the actual state of the target resource

        * compare the states

        * if necessary, change the system to enforce the state

    * events

        * if Puppet changes a resource, it will log those changes as events

            * these events will appear in the agent’s log and in the run report

    * parse-order independence

        * resources are not applied in the order they are written

            * Puppet will apply the resources in whatever way is most efficient

    * scope independence

        * resources are not subject to scope

    * containment

        * resources may be contained by classes and defined types

* Special attributes

    * name

        * can specify the name of the provider per platform (ntpd on RedHat and ntp on Debian; the title could be ntp)

    * ensure

        * manages the most fundamental aspect of the resource on the target system

* Condensed forms

    * array of titles

        * file { ['/etc',            '/etc/rc.d',            '/etc/rc.d/rc6.d']:      ensure => directory,      owner  => 'root',      group  => 'root',      mode   => '0755',    }

    * semicolon after attribute block

        * file {      '/etc/rc.d':        ensure => directory,        owner  => 'root',        group  => 'root',        mode   => '0755';      '/etc/rc.d/init.d':        ensure => directory,        owner  => 'root',        group  => 'root',        mode   => '0755'; }

    * resource defaults

* Adding or Modifying attributes

    * amending attributes with a reference

        * file {'/etc/passwd':      ensure => file,    }    File['/etc/passwd'] {      owner => 'root',      group => 'root',      mode  => '0640',    }

    * amending attributes with a collector

        * class base::linux {      file {'/etc/passwd':        ensure => file,      }      ...    }    include base::linux    File <| tag == 'base::linux' |> {      owner => 'root',      group => 'root',      mode  => '0640',    }

[Language: Relationships and Ordering](https://docs.puppet.com/puppet/4.6/reference/lang_relationships.html)

* Syntax

    * relationship metaparameters

        * before

            * causes a resource to be applied **before** the target resource

        * require

            * causes a resource to be applied **after** the target resource

        * notify

            * causes a resource to be applied **before** the target resource

            * the target resource will refresh if the notifying resource changes

        * subscribe

            * cause a resource to be applied **after** the target resource

            * the subscribing resource will refresh if the target resource changes

    * chaining arrows

        * -> (ordering arrow)

            * applies the resource on the left before the right

        * ~> (notification arrow)

            * refreshes the event on the right if anything changes

        * chained collectors

            *    Yumrepo <| |> -> Package <| |>

            * This example would apply all yum repository resources before applying any package resources, which protects any packages that rely on custom repos.

    * the require function

        * declares a class and causes it to become a dependency of the surrounding container

        * example

            * class wordpress {      require apache      require mysql      ...    }

            * The above example would cause every resource in the apache and mysql classes to be applied before any of the resources in the wordpress class.

* Behavior

    * ordering and notification

        * an ordering relationship ensures that one resource will be managed before another

        * a notification relationship does the same, but also sends the latter resource a refresh event

    * refreshing

        * only built-in types that support refresh are **service, mount**, and **exec.**

            * service

                * restarts the service

            * mount

                * unmounting and then mounting their volume

            * exec

                * usually do not refresh

                * refreshonly => true

                    * causes the exec to never fire unless it receives a refresh event.

    * autorequire

        * creates an ordering relationship without the user explicitly stating one

        * done when applying a catalog

    * parse-order independence

        * relationships are not limited by parse-order

        * you can declare a relationship with a resource before that resource has been declared

[Langauge: Resource Defaults](https://docs.puppet.com/puppet/4.6/reference/lang_defaults.html)

* Lets you set default attribute values for a given resource type

* Syntax example

    * Exec {      path        => '/usr/bin:/bin:/usr/sbin:/sbin',      environment => 'RUBYLIB=/opt/puppet/lib/ruby/site_ruby/1.8/',      logoutput   => true,      timeout     => 180,    }

[Language: Variables](https://docs.puppet.com/puppet/4.6/reference/lang_variables.html)

* Syntax

    * assignment

        * $content = "some content\n"

    * resolution

        * file {'/tmp/testing':      ensure  => file,      content => $content,    }    $address_array = [$address1, $address2, $address3]

    * interpolation

        * $rule = "Allow * from $ipaddress"    file { "${homedir}/.vim":      ensure => directory,      ...    }

    * appending assignments

        * $ssh_users = ['myself', 'someone']    class test {      $ssh_users += ['someone_else']    }

* Behavior

    * scope

        * area of code where a given variable is visible

    * accessing out-of-scope variables

        * use fully qualified names 

            * $vhostdir = $appache::params::vhostdir

    * no reassignment

        * the value is dependent on the scope

        * the value can be different at a lower scope

    * variables are parse-order dependent

[Language: Tags](https://docs.puppetlabs.com/puppet/latest/reference/lang_tags.html)

* Assigning tags to resources

    * automatic tagging

        * every resource automatically receives the following tags

            * its resource type

            * the full name of the class or defined type

            * every namespace segment 

    * containment

        * tags are passed along by containment

            * a resource will receive all of the tags from the class and or defined type that contains it

            *  In the case of nested containment (e.g. a class that declares a defined resource, or a defined type that declares other defined resources), a resource will receive tags from all of its containers.

    * tag metaparameter

        * accepts a single tag or an array

    * tag function

        * assigns tags to the surrounding container and all of the resources it contains

        * class role::public_web {  tag 'us_mirror1', 'us_mirror2'  apache::vhost {'docs.puppetlabs.com':    port => 80,  }  ssh::allowgroup {'www-data': }  @@nagios::website {'docs.puppetlabs.com': }}

* Using tags

    * collecting resources

        * tags can be used as an attribute in the search expression of a resource collector

            * most useful for realizing virtual and exported resources

            * realizing

                * realize(User['deploy'], User['zleslie'])

[Language: Facts and Built-in Variables](https://docs.puppet.com/puppet/4.6/reference/lang_facts_and_builtin_vars.html)

* Puppet collects system information with Facter

    * build-in core facts

    * any custom facts or external facts

* Data types

    * 4.6 version of Puppet supports facts values of any data type

    * handling string-only facts

        * older versions of Puppet would always convert all fact values to strings

        * third-party modules such as stdlib can help

* Accessing facts from Puppet code

    * classic $fact_name facts

        * appear in Puppet as top-scope variables

        * works in all versions of Puppet

        * not immediately obvious you’re using a fact

    * the $facts[‘fact_name’] hash

        * may or may not be enabled by default

            * trusted_node_data

        * more readable and maintainable code

        * only works with Puppet 3.5 or later and only when enabled

[Language: Scope](https://docs.puppet.com/puppet/4.6/reference/lang_scope.html)

* Scope basics

    * specific area of code which is partially isolated from other areas of code

        * limited reach of variables and resource defaults

    * any given scope has access to its own contents and also receives additional contents from its parent scope

    * scope does not limit the reach of

        * resource titles (global)

        * resource references

    * top scope

        * code that is outside any class definition, type definition, or node definitions. 

        * also facter

    * node scope

        * code that is inside a node definition

    * local scope

        * code inside a class definition or defined type

* More details

    * scope of external node classifier data

        * variables provide by the ENC are set at top scope

        * all classes assigned by an ENC are declared at node scope

    * named scopes and anonymous scopes

        * a class definition creates a named scope

        * top scope is also a named scope (empty string or $::)

        * node scopes and the local scopes created by defined resources are anonymous and cannot be directly referenced

* Scope Lookup Rules

    * The scope lookup rules determine when a local scope becomes the parent of another local scope

    * Two sets of scope lookup rules

        * static scope

            * parent scopes are only assigned by class inheritance (inherits keyword).

            * any derived class receives the contents of its base class in addition to the contents of node and top scope

            * all other local scopes have no parents

        * dynamic scope

            * parent scopes are assigned by both inheritance and declaration

            * each scope has only one parent

            * the parent of a derived class is its base class

            * the parent of any other class or defined resource is the first scope in which it was declared

            * example:

            * include dynamic    class dynamic {      $var = "from dynamic"      include included    }    class included {      notify { $var: } # dynamic lookup will end up finding "from dynamic"                       # this will change to being undefined    }

* Messy under-the-hood details

    * node scope only exists if there is at least one node definition in the site manifest

[Language: Conditional Statements](https://docs.puppet.com/puppet/4.6/reference/lang_conditional.html)

* if statement

    *   if str2bool("$is_virtual") {      warning('Tried to include class ntp on virtual machine; this node may be misclassified.')    }    elsif $operatingsystem == 'Darwin' {      warning('This NTP module does not yet work on our Mac laptops.')    }    else {      include ntp    }

* unless

    * unless $memorysize > 1024 {      $maxclient = 500    }

* case statement

    * case $operatingsystem {      'Solaris':          { include role::solaris }      'RedHat', 'CentOS': { include role::redhat  }      /^(Debian|Ubuntu)$/:{ include role::debian  }      default:            { include role::generic }    }

* selector

    * $rootgroup = $osfamily ? {        'Solaris'          => 'wheel',        /(Darwin|FreeBSD)/ => 'wheel',        default            => 'root',    }    file { '/etc/passwd':      ensure => file,      owner  => 'root',      group  => $rootgroup,    }

[Language: Expressions](https://docs.puppet.com/puppet/4.6/reference/lang_expressions.html)

* Comparison operators

    * ==

        * returns true if the operands are equal

        *  if $::osfamily == ‘Debian’ { } 

    * !=

        * not equal

        * if $::kernel != ‘Linux’ { } 

    * <, >, <=, >=

        * if $::uptime_days > 365 { }

    * =~ 

        * Regex match compress a string (left operator) with a regular expression operator and resolve true if it maches. 

        * if $::ipaddress =~ /^10\./ { } 

    * !~

        * Regex not match 

        * Resolves false if operands match	

    * in

        * resolves to true if the right operand contains the left operand

* Boolean operator

    * and

        * resolves to true if both operands are true

    * or

        * resolves to true if either operand is true

    * ! (not)

        * resolves to true if the operand is false

        * or resolves to false if the operand is true 

* Arithmetic operators

    * + addition

    * - subtraction

    * / division

    * * multiplication

    * % modulo

        * resolves to the remainder of dividing

    * <<  (left shift)

    * >> right shift

[Language: Function](https://docs.puppet.com/puppet/4.6/reference/lang_functions.html)[s](https://docs.puppet.com/puppet/4.6/reference/lang_functions.html)

* Functions

    * pre-defined chunks of Ruby code which run during compilation

    * most functions return values or modify the catalog

    * Puppet includes several built-in functions, and more are available in modules on the Puppet Forge.

    * You can also write custom functions and put them in your modules

#### Identify the core resource types

[Type Reference](https://docs.puppet.com/puppet/4.6/reference/type.html)

* Most common resource types

    * augeas

    * cron

    * exec

    * file

    * filebucket

        * backup location

    * group

    * host

        * /etc/host entry

    * mount

    * notify

    * package

    * service

    * stage

    * user

#### Demonstrate knowledge of classes and defines

[Language: Classes](https://docs.puppet.com/puppet/4.6/reference/lang_classes.html)

* Defining classes

    * syntax

        * class base_class::subclass (	$optional_params = ‘something’,){	type { ‘title’:		attribute => ‘value’,	}}

    * parameters

        * allow a class to request external data

    * location

        * class definitions should be stored in modules

            * under the manifests/ directory

        * Puppet is automatically aware of classes in modules and can autoload them by name

    * containment

        * a class contains all of its resources

    * auto-tagging

        * every resource in a class gets automatically tagged

    * inheritance

        * classes can be derived from other classes using the *inherits* keyword

        * the base class becomes the parent scope of the derived class. 

        * the new class receives a copy of all of the base class’s variables and resource defaults

        * code in the derived class can override any resource attributes at the base class

    * declaring a class

        * include like

            * functions

                * include

                * require

                * contain

                    * to be used inside another class definition

                    * it declares one or more classes, then causes them to become contained by the surrounding class

                * hiera_include

            * can safely declare a class multiple times

        * resource like

            * class something { }

            * supports parameters

L[earning Puppet - Defined Types](https://docs.puppetlabs.com/learning/definedtypes.html)

* Defined resource types

    * blocks of puppet code that can be evaluated multiple times with different parameters

    * example:

        * # /etc/puppetlabs/puppet/modules/apache/manifests/vhost.ppdefine apache::vhost (Integer $port, String[1] $docroot, String[1] $servername = $title, String $vhost_name = '*') {  include apache # contains Package['httpd'] and Service['httpd']  include apache::params # contains common config settings  $vhost_dir = $apache::params::vhost_dir  file { "${vhost_dir}/${servername}.conf":    content => template('apache/vhost-default.conf.erb'),      # This template can access all of the parameters and variables from above.    owner   => 'www',    group   => 'www',    mode    => '644',    require => Package['httpd'],    notify  => Service['httpd'],  }}

### Modules

#### Describe how to use modules from the Forge

[Installing Modules](https://docs.puppetlabs.com/puppet/latest/reference/modules_installing.html)

* About puppet module

    * Puppet Forge

        * a repository of modules written both by Puppet and by the Puppet user community.

    * tool provides a command line interface for managing modules from the Forge.

* Finding Forge modules

    * You can browse the Puppet Forge by Web

    * puppet module search SEARCHTERM

* Installing modules

    * puppet module install author-modulename

        * --target-dir

            * specify a target directory for installation

        * --environment

            * install into a different environment

        * --module_repository

        * can also specify a tarball

* List modules

    * puppet modules list

* Uninstalling modules

    * puppet module uninstall MODULENAME

#### Demonstrate knowledge of module structure

[Module Fundamentals](https://docs.puppetlabs.com/puppet/latest/reference/modules_fundamentals.html)

* Modules

    * self-contained bundles of code and data

    * you can download pre-built modules from the Puppet Forge

* Using modules

    * Puppet autoloads modules based on directory

    * can automatically load plugins from modules

* Module layout

    * <MODULE NAME>

        * manifests

        * files

        * templates

        * lib

        * facts.d

        * examples

        * spec

    * puppet module generate AUTHOR-MODULENAME

        * generates skeleton layout

[Documenting Modules](https://docs.puppet.com/puppet/4.6/reference/modules_documentation.html)

* README template

    * puppet module generate creates a README .md template

    * overview and description sections

        * overview should be the first things a user will read

        * briefly address what the module is working with and what it does

    * module description

        * goes into slightly more description

    * setup section

        * gives a user the basic steps to successfully get their module functioning

    * usage section

        * address how to solve some general problems with the module and include code examples

    * reference section

        * small table of contents that first list the public classes, defines, and resource types of your module

    * limitations

        * call out surprise incompatibilities

    * development

        * ground rules for contributing

* README style notes

    * general principles of READMES

        * write for both web and terminal viewing

        * limit the number of external links

        * scannability is key

    * style and formatting

        * the module name is always lowercase

        * public classes are intended to be tweaked, changed, or otherwise interacted with by the user

        * every parameter should have a description that starts with an action verb

        * params should be listed alphabetically

        * every param should specify what the valid inputs or values are

        * you should mark parametres as required or option

        * < > do not render in markdown

#### Identify module authoring best practices

[Beginner's Guide to Modules](https://docs.puppetlabs.com/guides/module_guides/bgtm.html)

* Giving your module purpose

    * defining the range of your module’s work helps you avoid accidentally creating a sprawling monster of a module

    * the module should have one area of responsibility

    * To help plan

        * What task do you need your module to accomplish?

        * What work is your module addressing?

        * What high function should your module have within your Puppet environment?

        * never answer any of the above questions with **and**

* Module structure

    * the ideal module is one that is designed to manage a single piece of software

        * class design

            * "good software is comprised of many small, easily tested things composed together"

            * a good module is small self-contained classes

            * module (base)

                * interface point and ought to be the only parametrized class if possible

            * module::install

                * all resources related to getting the software the module manages onto the node

            * module::config

                * the resources related to configuring the installed software

            * module::service

                * the remaining service resources, and anything related to the running state of the software

        * parameters

            * form the public API of your module

        * ordering

            * base your requires, befores, and other ordering-related dependencies on classes rather than resources

            * containment and anchoring

                * classes do not automatically contain the classes they declare

        * dependencies

            * include the dependent modules in the init.pp

* Module testing

    * rspec-puppet

        * unit-testing framework for Puppet

        * syntax

            * it { should contain_file(‘configuration’) }

    * rspec-system

        * acceptance/integration testing framework that provisions, configures and uses various Vagrant virtual machines to apply your puppet module

    * serverspec

        * provides additional testing constructs (such as be_running and be_installed)

    * puppetlabs-spec-helper

        * gem that automates some of the tasks required to test modules

* Module versioning

    * use [SemVer 1.0.0](http://semver.org/spec/v1.0.0.html). I

* Module releasing

    * Puppet Forget

[Best Practices for Building Puppet Modules](https://puppetlabs.com/blog/best-practices-building-puppet-modules)

* Building a functional Puppet workflow

    * module structure

        * parameters are your API

        * inherit the ::params class

            * Syntax

                * class apache (	$port = $apache::params::port,	$user = $apache::params::user,) inherits apache::params {

            * Only recommended use of inheriting

        * keep your component modules generic

            * store the "company data" in Hiera

        * store your modules in version control

            * one repo per module method

                * individual development histories

                * per-module security settings

            * all modules in a single repos method

    * roles and profiles

        * profiles

            * technology-specific wrapper classes

                * groups Hiera lookups and class declarations into one functional unit

            * exists to give you a single class you can include that will will setup all the necessary bits for that piece of technology

            * do all hiera lookups in the profile

                * class profiles::wordpress {	$site_name = hiera(‘profiles::wordrpess::site_name’),…}

            * looking up parameters and declaring classes

        * roles

            * business-specific wrapper class

                * mapping of your machines names to the technology that should be ON them

                    * "DMZ repo machines"

                    * "app builder machines"

            * roles ONLY include profiles

            * every node is classified with **one role**

        * summary

            * roles abstract profiles

            * profiles abstract component modules

            * Hiera abstracts configuration data

            * component modules abstract resources

            * resources abstract the underlying OS implementation

### Using Puppet

#### Describe environments in Puppet

[About Environments](https://docs.puppetlabs.com/puppet/latest/reference/environments.html)

* Environments

    * isolated groups of Puppet agent nodes

    * Puppet master server can serve each environment with completely different manifests and modulepaths

* Assigning nodes to environments

    * use agent’s config file

    * ENC

    * Puppet Enterprise console

* Referencing the environment in manifests

    * $environment

[Configuring Directory Environments](https://docs.puppetlabs.com/puppet/latest/reference/environments_configuring.html)

* Global settings for configuring environments

    * five settings in puppet.conf

        * environmentpath

            * the list of directories where Puppet will look for environments

        * basemodulepath

            * lists directories of global modules that all environments can access

        * default_manifest

            * specifies the main manifest for any environment that doesn’t set a manifest value in environment.conf

        * disable_per_environment_manifest

            * lets you specify that **all **environments should use a shared main manifest. 

            * default_manifest must be set to an absolute path

        * environment_timeout

            * how often Puppet will refresh information about environments

[Enabling Directory Environments in Puppet Enterprise](https://docs.puppet.com/puppet/4.6/reference/environments_configuring.html)

* Directory environments are disabled by default in PE 3.3

* To enable in Puppet Enterprise

    * edit the config file

    * create at least one directory environment

    * Create a direcotry environment

        * must have a production environment at least

* Enabling Directory Environments in Open Source Puppet

    * same as Enterprise

[Assigning Nodes to Environments](https://docs.puppetlabs.com/puppet/latest/reference/environments_assigning.html)

* By default, all nodes are assigned to default environment named production

* Two ways to assign nodes

    * via ENC or node terminus

    * via agent node’s puppet.conf

* Assigning environments via an ENC

    * The interface to set the environment for a node will be different for each ENC

    * When writing an ENC, ensure that the **environment:** key is set in the YAML output that the ENC returns

* Assigning environment via the agent’s config

    * environment setting in either the agent or main config

[Environments: Suggestions for Use](https://docs.puppetlabs.com/puppet/latest/reference/environments_suggestions.html)

* Permanent test environments

    * relatively stable group of test nodes in permanent test environment

    * resemble production infrastructure in miniature

* Temporary test environments

    * developers and admins can create temporary environments to test out a single change

    * fresh checkout from your version control into the $codedir/environments directory

[Environments: Limitations of Environments](https://docs.puppetlabs.com/puppet/latest/reference/environments_limitations.html)

* Plugins running on the master are weird

    * any plugins destined for the agent node (eg custom facts and custom resource providers) will work fine

        * but, plugins to be used by the Puppet master (functions, resource types, report processors, indirector termini) can get mixed up

* For best performance, you might have to change your deploy process

    * default value of environment_timeout setting has to be 0

    * for best performance, environment_timeout = unlimited should be set

        * you’ll need to refresh the Puppet master to make it notice new code

* Hiera configuration can’t be specified per environment

* Exported resources can conflict or cross over

    * nodes in one environment can accidently collect resources that were exported from another environment

        * compilation error due to identically titled resources

        * creation and management of unintended resources

[Environments and Puppet's HTTPS Interface](https://docs.puppetlabs.com/puppet/latest/reference/environments_https.html)

* Environments are embedded in Puppet’s HTTPS requests

    * Most of the HTTPS URLs used today by Puppet agent include an environment

* Controlling HTTPS access based on environment

    * The puppet master’s auth.conf file can use the environment of a request to help decide whether to authorize a request

#### Describe the lifecycle of a Puppet run

[Learning Puppet — Basic Agent/Master Puppet](https://docs.puppetlabs.com/learning/agent_master_basic.html)

* Two stages for config management

    * Compile a catalog

    * Apply the catalog

* The agent/master architecture

    * master server controls important configuration info

    * managed agent nodes request only their own configuration catalogs

    * basics

        * agent application usually runs as a background service

        * one or more servers run the master application

        * periodically, the agent sends facts to the Puppet master and requests a catalog

        * the master compiles and returns that node’s catalog

        * the agent then applies its catalog by checking each resource described

    * communications and security

        * communicate via HTTPS with client-verification

        * master provides an HTTP interface with various endpoints available

        * client-verified HTTPS means each master or agent must have an identifying SSL certificate

        * **puppet cert** command to inspect requests

* The stand-alone architecture

    * each managed server has its own complete copy of your configuration info

    * basics

        * managed nodes run the **puppet apply** application via a scheduled task or cron.

        * same process as agent/master after this

* Differences between agent/master and puppet apply

    * Principle of least privilege

        * in agent/master

            * each agent only gets its own configuration and is unable to see how other nodes are configured

        * in standalone

            * every node has access to complete knowledge about how your site is configured

            * potentially raises the risk of horizontal privilege escalation

    * Ease of centralized reporting and inventory

        * agent/master set up

    * Ease of updating configuration

    * CPU and memory usage on managed machines	

        * catalog compiled on master server

    * Need for a dedicated master server

    * Need for good network connectivity

    * Security overhead

#### Describe Puppet ecosystem component usage

[A New Era of Application Services at Puppet Labs](https://puppetlabs.com/blog/new-era-application-services-puppet-labs)

* Trapperkeeper

    * new Clojure framework

        * written for hosting long-running applications and services

        * high performance server-side architecture 

        * open source

* The Java Virtual Machine

    * JVM

        * ideal platform to use for long-running server side applications

        * fast, stable, and reliable platform for web servers, message queues, and other services

        * wealth of existing tools, libraries, and frameworks that serve as tremendously valuable building blocks

        * lots of performance profilers available

* Clojure

    * relatively new language

    * dynamically typed language

    * functional programming language

        * code written is very modular, composable, reusable and easy to reason about

    * it’s idempotent, like Puppet

    * simple, powerful concurrency primitives

        * write multithreaded code without the complexity of traditional primitives like locks

    * high-performance immutable data structures

    * Interoperability with the Java language

* Broadening our horizons

    * the ability to configure and control which parts of the system are loaded at run-time

    * the ability to compose modular bits of functionality

    * a way to cohesively manage the lifecycle of the various components making up an application

    * a common logging mechanism that wouldn’t need to be set up by each component

    * a web server flexible enough to load multiple web apps

* Alternatives to build it here

    * OSGi

        * provides a Service Registry that can be used to

            * define services

            * load them together into the JVM

            * manage dependencies between them

            * defined via Java interfaces

            * provides a lot of very powerful but very complex functionality that allow you to hot-swap service implementations in a running JVM

    * JBoss / Immutant

        * JBoss

            * a popular Java enterprise application server

        * Immutant	

            * provides Clojure bindings for most of the JBoss functionality

            * large deployment artifact

            * ships with predefined technology choices

                * JBoss web server, HornetMQ message queue

                * Infinispan key/value cache

            * needed something lightweight

[Subsystems: Agent/Master HTTPS Communications](https://docs.puppet.com/puppet/3.7/reference/subsystem_agent_master_comm.html)

* Persistent Connections / Keepalive

    * Puppet will try to re-use connections in order to reduce TLS overhead by sending Connection: Keep-Alive in the HTTP request

    * Puppet will only cache verified HTTPS connections

* Steps in catalog request

    * Check for Keys and Certificates

        * Does the agent have a private key at $ssldir/private_keys/<name>.pem?

            * If no, generate one.

        * Does the agent have a copy of the CA certificate at $ssldir/certs/ca.pem?

            * Does the agent have a private key at $If no, fetch it. (Unverified GET request to /certificate/ca. Since the agent is retrieving the foundation for all future trust over an untrusted link, this could be vulnerable to MITM attacks, but it’s also just a convenience; you can make this step unnecessary by distributing the CA cert as part of your server provisioning process, so that agents never ask for a CA cert over the network. If you do this, an attacker could temporarily deny Puppet service to brand new nodes, but would be unable to take control of them with a rogue Puppet master.)

        * Does the agent have a signed certificate at $ssldir/certs/<name>.pem?

            * If yes, skip the following section and continue to "request node object."

            * (If it has a cert but it doesn’t match the private key, bail with an error.)

    * Obtain a Certificate (if necessary)

        * Try to fetch an already-signed certificate from the master

            * if it gets one, skip the rest of this section

            * doesn’t match then bail

        * Determine whether the agent has already requested a certificate signing

            * $ssldir/certificate_requests/<name>.pem

                * if exists, then bail due to step waiting on master

        * Double check with the master whether the agent has already requested a certificate signing

    * If the agent never requested a certificate, request one now

        *  (Unverified PUT request to /certificate_request/<name>.)

* Request Node object and switch environments

    * Do a GET request to /node/<name>

        * If successful, read the environment from the node object

        * if unsuccessful, continue using the environment from the agent’s config file

* Fetch plugins (if pluginsync is enabled on the agent)

    * Do A GET request to /file_metadatas/plugins with recurse=true and links=manage.

    * Check whether any of the plugins need to be downloaded

        * If so, GET request to /file_content/plugins/<file> for each one.

* Request catalog while submitting facts

    * Do a POST request to /catalog/<name>, where the post data is all of the node’s facts encoded as JSON. Receive a compiled catalog in return.

* Make file source requests while applying catalog

    * Do a GET request to /file_metadata/<something>.

    * Compare the metadata to the state of the file on disk.

        * If it is in sync, move on to the next file source.

        * If it is out of sync, do a GET request to /file_content/<something> for the current content.

* Submit report (if report is enabled on the agent)

    * Do a PUT request to /report/<name>. The content of the PUT should be a Puppet report object in YAML format.

[Subsystems: Catalog Compilation](https://docs.puppet.com/puppet/4.6/reference/subsystem_catalog_compilation.html)

* Background info

    * Puppet agent uses a document called a catalog which is downloaded from the master server

    * Manifests are concise, and resolving these on the master allows Puppet to

        * Separate privileges

            * Each individual node has little to no knowledge about other nodes

        * Reduces the agent’s resource consumption

        * Simulate changes

            * option to simulate changes with --noop

        * Record and query configurations

    * Puppet apply will compile its own catalog and then apply it

* Information sources

    * Puppet compiles a catalog using

        * agent-provided data

            * agent provides

                * their name, which is embedded in the request URL

                * their certificate, which contains their certname

                * their facts

                * their requested environment

        * external data

            * data from an ENC or other node terminus

            * the data arrives in the form of a node object which contains:

                * classes that should be assigned to the node and parameters to configure those classes

                * extra top-scope variables

                * an environment

            * data from other sources

                * exported resources queried from PuppetDB

                * results of functions

        * Puppet manifests (also templates and sources)

            * main manifest

            * modules downloaded from Puppet Forge

            * modules written specifically for your site

* Process of Catalog Compilation

    * Step 1: Retrieve the Node Object

        * once the master has the agent-provided info, it asks its configured node terminus for a node object

            * by default uses the plain node terminus, which just returns a blank node object

            * next most common is the exec mode terminus

                * requests data from an ENC

            * less common is the ldap node terminus

                * fetches ENC like information from an LDAP database

    * Step 2: Set Variables from the Node Object, from Facts, and from the Certificate

        * Any variables provided by the node object will now be set as top-scope variables

        * The nodes facts are also set as top-scope variables

        * if trusted_node_data = true

            * facts will also be set in the protected $facts hash

            * data from the node’s certificate will be site in the protected $trusted hash

    * Step 3: Evaluate the Main Manifest

        * Puppet then parses the main manifest

        * Loads and evaluates classes from modules

            * automatically loads the manifests from its collection of modules

    * Step 4: Evaluate Classes from the Node Object

        * load from modules and evaluate any classes that were specified by the node object

[PuppetDB 4.2 Overview](https://docs.puppet.com/puppetdb/4.2/)

* Install it now

    * Review [the system requirements below](https://docs.puppet.com/puppetdb/4.2/#system-requirements) (and, optionally, [our scaling recommendations](https://docs.puppet.com/puppetdb/4.2/scaling_recommendations.html)).

    * Choose your installation method:

        * [Easy install](https://docs.puppet.com/puppetdb/4.2/install_via_module.html) using the PuppetDB puppet module on our recommended platforms

        * [Install from packages](https://docs.puppet.com/puppetdb/4.2/install_from_packages.html) on our recommended platforms

        * [Advanced install](https://docs.puppet.com/puppetdb/4.2/install_from_source.html) on any other *nix

* What data?

    * PuppetDB stores

        * most recent facts from every node

        * most recent catalog for every node

        * Optionally, 14 days of event reports

    * inventory of metadata that is searchable

#### Describe how to configure a Puppet master

[Configuration: How Puppet is Configured](https://docs.puppetlabs.com/puppet/latest/reference/config_about_settings.html)

* How Puppet Loads Settings

    * Common settings

        * For agents

            * basics

                * server - puppet master server

                * certname - node’s certificate name

                * environment  - env. to request from master

            * run behavior

                * noop - simulate changes

                * priority - "nice" puppet agent

                * report - whether to send reports

                * tags - only include resources with certain tags

                * trace,profile,graph, and show_diff - debugging or learning more about an agent run

                * usecacheonfailure - fail back to known catalog

                * ignoreschedules - self explanatory

                * prerun_command and postrun_command

            * service behavior

                * runinterval - how often to do a Puppet run

                * waitforcert - seconds to keep waiting

            * when running from cron

                * splay and splaylimit - spreads out agent runs

                * daemonize - whether to daemonize

                * onetime = whether to exit after finishing the current run,** set to true when running from cron**

        * For master servers

            * basics

                * dns_alt_names 

                * environment_timeout

                * environmentpath

                * basemodulepath

                * reports

            * puppet server related settings

                * puppet-admin

                * jruby-puppet

                * JAVA_ARGS

            * Rack related settings

                * ssl_client_header and ssl_client_verify_header

                * always_cache_features

            * extensions

                * node_terminus and external_nodes - ENC settings

                * storeconfigs and storeconfigs_backend - used for PuppetDB

                * catalog_terminus - enables the optional static compiler

            * CA settings

                * ca - whether to act as a CA

                * ca_ttl - how long the signed cert is valid for

                * autosign - auto signing certificates

    * Settings are loaded on startup

        * a command or service will only read its settings once

        * settings changes require restart

    * Settings can be set on the command line

    * Settings can be set in the main config file

        * puppet.conf

    * Settings have default values

* Main setting vs extra config files

    * there are a bunch of other config files available like 

        * puppetdb.conf

        * auth.conf

[Puppet Server: Configuration](https://docs.puppetlabs.com/puppetserver/1.0/configuration.html)

* Configuration files

    * global.conf

    * webserver.conf

    * web-routes.conf

    * puppetserver.conf

    * auth.conf

    * master.conf

    * ca.conf

* Logging

    * Server logging is routed through JVM Logback library

    * the default logback config file is at /etc/puppetserver/logback.xml

    * global.conf

* HTTP Traffic

    * Puppet Server logs HTTP traffic in a format similar to Apache

    * the following information is logged for each HTTP request

        * remote host

        * remote log name

        * remote user

        * date of the logging event

        * URL requested

        * status code of the request

        * response content length

        * remote IP address

        * local port 

        * elapsed time to serve the request

* Service Bootstrapping

    * Puppet Server is built on top of the Clojure application framework, Trapperkeeper

        * provides the ability to enable or disable individual services that an application provides

    * service bootstrap configuration files are in two locations

        * /etc/puppetlabs/puppetserver/services.d/

        * /opt/puppetlabs/server/apps/puppetserver/config/services.d

### Puppet Internals

#### Describe the purpose of types and providers

[Custom Types](https://docs.puppetlabs.com/guides/custom_types.html)

* Types and Providers

    * when making a new type, you will make two things

        * the "type" itself, which is a model of the resource type

            * defines what parameters are available

            * handles input validation

            * determines what features a provider can (or should) provide

        * one or more providers for that type

            * implements the type by translating its capabilities into specific operations on a system

* Deploying and using Types and Providers

    * to use new types and providers

        * the type and providers must be present in a module on the puppet master

        * like other types of plugin such as custom functions and custom facts, they should go in the /lib directory

            * type files should be located at /lib/puppet/type<TYPE NAME>.rb

            * provider files should be located at /lib/puppet/provider/<TYPE NAME>/<PROVIDER NAME>.rb

        * if using agent/master

            * node must have its pluginsync setting in puppet.conf set to true

* Types

    * when defining a type, focus on what the resource can do, not how it does it

    * creating a type

        * created by calling the newtype method on the Puppet::Type class

            *  # lib/puppet/type/database.rb    Puppet::Type.newtype(:database) do      @doc = "Create a new database."      # ... the code ...    end

            * the name of the type is the only required argument to newtype

            * also requires a block of code with {...} or do .. end syntax

    * type documentation

        * write strings describing the resource type and assign it to the @doc instance

            * Puppet::Type.newtype(:database) do      @doc = %q{Creates a new database. Depending        on the provider, this may create relational        databases or NoSQL document stores.        Example:            database {'mydatabase':              ensure => present,              owner  => root,            }      }    end

    * properties and parameters

        * properties and parameters will become the resource attributes available when declaring a resource of the new type

        * properties

            * Properties should map more or less directly to something measurable on the target system

            * defines how the resource really works

            * Puppet::Type.newtype(:database) do      ensurable      newproperty(:owner) do        desc "The owner of the database."        ...      end    end

        * Parameters change how Puppet manages a resource, but do not necessarily map directly to something measurable

            * parametres never result in methods being called on providers

            * newparam(:name) do      desc "The name of the database."    end

[Provider Development](https://docs.puppetlabs.com/guides/provider_development.html)

* About

    * core of Puppet’s cross-platform support is via Resource Providers

        * basically back-ends that implement support for a specific implementation of a given resource type

        * main job is to wrap client-side tools, usually by just calling out to those tools with the right information

* Declaration

    * providers are always associated with a single resource type, so they are created by calling the **provide** method on that resource type.

    * **provide **method takes three arguments plus a block

        * the first argument must be the name of the provider, as a :symbol

        * the option :parent argument should be the name of a parent class

        * the optional :source argument should be a symbol

    * parent classes

        * base provider

            * a provider can inherit from a  base provider

        * another provider of the same resource type

            * providers can also specify another provider as their parent

        * a provider of any resource type

#### Describe Puppet’s use of SSL certificates

[Certificates and Security](http://projects.puppetlabs.com/projects/1/wiki/certificates_and_security)

* Puppet’s network communications and security are all based on HTTPS

* What is public key cryptography?

    * family of algorithms and practices for encrypting and verifying information

    * two participants can securely exchange and verify information without sharing any secret key ahead of time.

    * key pairs

        * each participant possesses a key pair, which consists of a public key and a private key

            * any public key has one and only one private key 

            * a private key can’t be reverse-engineered from its corresponding public key

    * key pairs can encrypt information

        * if you have the *public* part of a given key pair, you can encrypt a message so that it can only be deciphered by whoever possesses the corresponding private key

    * key pairs can sign information

        * if you have the private key, you can digitally sign a message

* What are certificates and PKI?

    * certificates

        * cryptographic identification document that contains

            * a public key

            * metadata about the certificate and its owner

            * a signature from the CA, which is a trusted third party

            * these parts work together to form a unit of trust

                * The public key lets you determine whether the entity you’re talking to possesses the corresponding private key, and enables secure communication with them.

                * The metadata tells you who that entity is, what they are allowed to do, and how long their certificate is valid.

                * The signature proves that someone you trust has done a background check on the key pair’s owner, and has gone on record stating that the metadata in the certificate is correct. It also prevents tampering with any of the information in the certificate: if any part is changed, the signature will fail to validate.

        * certificate storage

            * certificates are usually stored in some encoded format

            * most common format is PEM

        * certificate authorities

            * a trusted person or institution that controls a key pair

        * certificate signing requests

            * participants can send certificate signing requests to the CA

            * specially formatted cryptographic document that contains the applicant’s public key and any metadata

    * certificate revocation checking

        * trusted certificates sometimes need to become untrusted

        * certificate revocation lists

            * contains the serial numbers of any certificates that should no longer be trusted

            * this is the method of revocation checking that Puppet uses

        * online certificate status protocol

            * CRLs are relatively expensive to update over the network

            * OCSP is a protocol that assumes participants are always on a network

            * OCSP Stapling

                * requires frequent extra network calls to third parties

                * leaves revocation checks to the client

    * certificate lifespans

        * each certificate has a period of time for which it is valid

    * certificates and Puppet: The Puppet CA

        * The CA consists of the following components

            * several HTTPS services provided by the puppet master

            * on-disk files, which include the CA certificate, the CA private key, any stored CSRs, an inventory of previously signed certificates

            * command-line tools for managing CSRs/Certificates

            * HTTPS services that allow certificates to be signed and revoked

    * summary

        * Trust originates outside the PKI, and is a prerequisite for joining it: if you’re participating, you’ve agreed to trust a specific person or institution to diligently check other participants’ credentials. Sometimes this agreement is active; other times, it’s tacit, like when you install a web browser.

        * A certificate has three parts: public key, metadata, and signature from the CA. Without all three, it’s not a certificate.

        * The CA issues all certificates. If it’s a certificate at all, the CA has seen it and approved it.

        * Because the CA approves all certificate metadata, participants don’t have to keep a list of all the public keys they’ll need to know about; instead, they can just trust any valid certificate they are shown.

        * Because certificates include public keys, only their rightful owner can present them as ID. A stolen cert is inert without a stolen private key.

        * The CA can also revoke certificates, but that only works if everybody regularly checks for revoked certificates (via a traditional CRL or more modern means). This is even harder to ensure than it sounds.

        * Puppet has built-in tools to make managing a CA easier. These are covered in other documentation.

* What is TLS/SSL?

    * TLS is a protocol that uses an X.509 PKI to create secure and authenticated channels of network communication

        * SSL is an older version

    * participants and their tools

        * SSL always has two participants

            * a client

                * initiates the connection

            * server

                * accepts the connections

                * must always have a certificate signed by a CA that the client trusts

    * starting an SSL connection	

        * after a client starts the process, an SSL connection involves the following procedures

            * the client and server negotiate to figure out which cipher and protocol to use

            * the server presents its certificate

                * the client software validates that certificate based on its list of trustworthy CAs

            * the client sends a temporary session key to the server, encrypted so that only the owner of the server certificate can read it

                * both client and server use that session key to encrypt all subsequent traffic in the connection using a symmetric cipher

        * specific advantages of an SSL connection

            * both parties

                * access to an encrypted communication channel

            * the client

                * it knows it’s talking to the rightful owner of the certificate

                * it knows the CA verified any metadata in the certificate

            * the server

                * if client authentication is enabled

                    * the server knows it’s talking to the owner of the client certificate

* What is HTTPS

    * SSL is generally used to wrap another protocol such as HTTP

    * HTTPS and Puppet

        * Puppet uses HTTPS for all of its traffic

        * requires certificate-based PKI which in turn requires public key cryptography

        * client authentication

            * most of the endpoints require client authentication

            * master server and agent nodes must have their own certificates

        * ports

            * 8140 since it’s not similar to web traffic

    * persistence of SSL/certificate data in HTTPS applications

        * most SSL implementations have some means to publish connection and certificate data, so it can be used by higher layers of the protocol stack.

    * SSL Termination and Proxying

        * large server-side HTTPS applications often need to be split into multiple semi-independent components or services

![image alt text](image_0.png)

* X.509 Certificate anatomy

    * A master or agent certificate

        * certificates used by puppet master servers and agent nodes are essentially the same

            * only difference is that the master certs sometimes contains alternate DNS names

    * Text output

        * openssl x509 -text -noout -in <file>

    * Sections

        * subject

            * owner of the certificate

            * contains

                * DN ( distinguished name)

                    * represents the subject's identity

                * CN ( common name)

                    * **CN is the only part of the DN used by Puppet**

                    * legal hostnames

        * issuer

            * DN of the CA that signed the certificate

        * validity period

        * alternative DNS names

        * CA permissions

            * This field states whether the certificate can be used to sign new certificates.

            * only a CA would have this set to yes

        * key usage

            * This defines the things the certificate can be used for. If you’ve read the series of background articles on SSL

            * The puppet master cert is also used by puppet agent running on the puppet master node, in order to request a catalog. (From itself, but rules are rules: it still uses HTTPS to do so.)

            * The puppet master server process can sometimes act as a client, requesting services provided by a PuppetDB server, a different puppet master server, or another HTTPS service.

            * In certain configurations (mostly the deprecated "puppet kick" feature), the puppet agent process can run an HTTPS server that listens for requests on port 8139.

        * puppet-specific certificate extensions

            * node specific information to be embedded in a certificate

    * Note that the CA certificate actually lacks all of the permissions granted to normal certificates. It cannot be used to:

        * Sign arbitrary messages

        * Encipher arbitrary messages

        * Pose as a web server

        * Pose as a web client

        * It can only be used to sign certificates and CRLs.

[Puppet Server: External CA Configuration](https://docs.puppetlabs.com/puppetserver/1.0/external_ca_configuration.html)

* Server is hosted by a Jetty web server, therefore, Rack-enabled web server configuration is irrelevant

* To disable the CA service

    * edit ca.cfg

        * uncomment the disable line

* Web server configuration

    * ssl-cert: The value of puppet master --configprint hostcert. Equivalent to the ‘SSLCertificateFile’ Apache config setting.

    * ssl-key: The value of puppet master --configprint hostprivkey. Equivalent to the ‘SSLCertificateKeyFile’ Apache config setting.

    * ssl-ca-cert: The value of puppet master --configprint localcacert. Equivalent to the ‘SSLCACertificateFile’ Apache config setting.

    * ssl-cert-chain: Equivalent to the ‘SSLCertificateChainFile’ Apache config setting. Optional.

    * ssl-crl-path: Equivalent to the ‘SSLCARevocationPath’ Apache config setting. Optional.

[SSL Configuration: External CA Support](https://docs.puppetlabs.com/puppet/latest/reference/config_ssl_external_ca.html)

* Supported external CA configurations

    * single self-signed CA which directly issues SSL certificates

    * single intermediate CA issued by a root self-signed CA

    * two intermediate CAs, both issued by the same root self-signed CA

        * One intermediate CA issues SSL certificates for Puppet master servers.

        * The other intermediate CA issues SSL certificates for agent nodes.

        * Agent certificates can’t act as servers, and master certificates can’t act as clients.

* General notes and requirements

    * Rack web server is required

        * The Puppet master must be running inside of a Rack-enabled web server

            * apache or nginx

    * disable the internal CA service

    * ensure that the certname will never change

    * put certificates/keys in place on disk

    * configure the web server

[SSL Configuration: Autosigning Certificate Requests](https://docs.puppetlabs.com/puppet/3.7/reference/ssl_autosign.html)

* CSRs, Certificates, and Autosigning

    * agents will submit a certificate signing request to the CA Puppet master and will retrieve a signed certificate once one is available

* Basic autosigning (autosign.conf)

    * whitelist of certificate names and domain name globs

    * enabling

        * autoasign = <whitelist file> in [main] of the Puppet masters puppet.conf

* Policy-based autosigning

    * external policy executable every time it receives a CSR

        * autoasign = <policy bin file> in [main] of the Puppet masters puppet.conf

### Classification

#### Describe classification

[Getting Started with Classification](https://docs.puppet.com/pe/2016.2/console_classes_groups_getting_started.html)

* What is classification?

    * in PE, you configure your nodes by assigning classes, parameters, and variables to them

    * node groups

        * powerful and scalable way to automate classification of your nodes and saves you a lot of manual work

        * main steps involved in dynamically classifying:

            * create node groups

            * create rules to dynamically add and remove nodes from node groups

            * assign classes to node groups

    * organizing node groups

        * start with high-level groups that reflect the business requirements of your organization

            * web servers

            * next you identify a subset of web servers with common characteristics

                * production vs development

[Puppet: Assigning Configurations to Nodes](https://docs.puppetlabs.com/pe/latest/puppet_assign_configurations.html)

* Roles and profiles: Introduction

    * most reliable way to build reusable, configurable, and refactorable system configuration

    * A summary of the roles and profiles method

        * two extra layers of indirection between your node classifier and your component modules

        * Separates code into three levels

            * component modules

                * normal modules that manage one particular technology

            * profiles

                * wrapper classes that use multiple component modules to configure a layered technology stack

            * roles

                * wrapper classes that use multiple profiles to build a complete system configuration

Example Roles/Profiles environment![image alt text](image_1.png)

[Grouping and Classifying Nodes](https://docs.puppetlabs.com/pe/latest/console_classes_groups.html)

* Types of node groups

    * two types of node groups

        * environment node groups

            * assign environments to nodes

        * classification node groups

            * assign classification data to nodes

* Preconfigured environment node groups

    * PE comes with several preconfigured node groups, including two environment node groups

        * production environment

            * matches all nodes and assigns the node to the production environment

        * agent-specified environment

            * no nodes by default

            * nodes that you add to this group use the environment from their config file

* Creating environment node groups

    * In the PE console, click **Nodes > Classification,** then click **Add group.**

    * Specify options for the new node group:

        * **Parent name** – Select** Production environment**. Every environment node group you add must be a descendent of this group. This lets the new group inherit the ability to override a member node’s agent-specified environment setting.

        * **Group name** – Enter a name that describes the role of this environment node group, for example, Test environment.

        * **Environment** – Select the environment that you want to assign to nodes that match this node group. If you haven’t created environments yet, you see only the **production** and **agent-specified** environments.

    * **Environment group **– Select this option.

    * Click Add.

* Creating classification node groups

    * In the PE console, click **Nodes > Classification**, then click** Add group.**

    * Specify options for the new node group:

        * **Parent name **– Select the name of the classification node group that you want to set as the parent to this node group. Classification node groups inherit classes, parameters, and variables from their parent node group. By default, the parent node group is the **All Nodes** node group.

        * **Group name** – Enter a name that describes the role of this classification node group, for example, Web Servers.

        * **Environment** – Specify an environment to limit the classes and parameters available for selection in this node group.

        * do not select environment group

    * Click Add

* Adding nodes to a node group

    * two ways to add nodes to a node group

        * create rules that match node facts (dynamic)

            * Click** Nodes > Classification,** then click the group that you want to add the rule to.

            * Select the **Rules** tab.

            * Specify rules for the fact, then click **Add rule**.

        * individually pin nodes to the node group (static)

            * On the **Classification** page, click the node group that you want to add the class to, and then click **Classes**.

            * Under **Add new class**, click the Class name field.

            * Click **Add class** and then click the commit button.

* Defining the data used by classes

    * setting class parameters

        * In **Classes**, click the **Parameter name** drop-down list under the appropriate class and select the parameter to add. The drop-down list shows all of the parameters that are available in the node group’s environment.

        * When you select a parameter, the **Value** field is automatically populated with the default value. To change the value, type the new value in the **Value** field.

    * setting variables

        * To access a node group’s variables, on the **Classification** page, select the node group.

        * Click **Variables**.

        * For **Key**, enter the name of the variable.

        * For **Value**, enter the value that you want to assign to the variable.

        * Click **Add variable**, and then click the commit button.

### Console

#### Describe RBAC

[Role-based Access Control](https://docs.puppet.com/pe/2016.2/rbac_intro.html)

* Role-based access control (RBAC) is used to manage user permissions

    * Permissions define what actions users can perform

        * can the user grant password reset tokens to other users who have forgotten their passwords?

        * can the user edit a local user’s metadata?

        * can the user edit class parameters in a node group?

    * Permissions are assigned to user roles rather than directly to users

    * Users inherit all of the permissions from each user role they are in

    * PE ships with four default user roles

        * Administrators

        * Code Deployers

        * Operators

        * Viewers

* External directories

    * PE can connect to external LDAP directories

    * PE supports OpenLDAP and Active Directory

* RBAC and activity services

    * access control is handled by the RBAC service

[Connecting PE to an External Directory Service](https://docs.puppet.com/pe/2016.2/rbac_ldap.html)

* Overview

    * PE connects to external LDAP directory services through PE’s RBAC service

    * you can use PE to

        * authenticate external directory users

        * authorize access of external directory users based on RBAC permissions

        * store and retrieve the groups and group membership information that has been set up in your external directory

* For exact steps, refer to online documentation

[RBAC Permissions](https://docs.puppetlabs.com/pe/3.7/rbac_permissions.html)

* Overview

    * provides a specific set of roles you can assign to users and groups

        * these roles contain permissions

    * permissions are additive

* Structure of permissions

    * structured as a triple of type, permission, and object

        * types

            * everything that can be acted on, such as node groups, users, or roles

        * permissions

            * what you can do with each type, such as create, edit, or view

        * objects

            * specific instances of the type

    * example administrator user role 

<table>
  <tr>
    <td>Type</td>
    <td>Permission</td>
    <td>Object</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>Node groups</td>
    <td>View</td>
    <td>PE Master</td>
    <td>Gives permission to view the PE Master node group.</td>
  </tr>
  <tr>
    <td>User roles</td>
    <td>Edit</td>
    <td>All</td>
    <td>Gives permission to edit all user roles.
</td>
  </tr>
</table>


[Creating and Managing Users and User Roles](https://docs.puppetlabs.com/pe/3.7/rbac_user_roles.html)

* Overview

    * User roles are sets of permissions you can apply to multiple users in PE.

    * RBAC lets you manage users, what they can create, edit, or view and what they can’t

* Create a new user

    * In the console, click **Access Control,** and then click **Users**.

    * On the **Users** page, in the **Full name** field, type in a user name.

    * In the **Login** field, type the user’s login information.

    * Click **Add local user**.

* Enable a user to log in (**required for first time login**)

    * Click the new local user in the **Users** list. The new user’s page opens.

    * On the upper-right of the page, click **Generate password reset**. A **Password reset link** message box opens.

    * Copy the link provided in the message and send it to the new user. Then you can close the message.

* Create a new user role

    * In the console, click **Access Control **and then click **User Roles**.

    * For **Name**, type a name for the role, and then for **Description**, type a description for the role.

    * Click** Add role.**

* Assign permissions to a User Role

    * In the list of user roles, click a user role that you want to add permissions to.

    * Click the **Permissions** tab.

    * In the **Type** box, select the type of object you want to assign permissions for, such as **Node groups**.

    * In the **Permission** box, select the permission you want to assign, such as **View**.

    * In the **Object** box, select the specific object you want to assign the permission to. For example, if you are setting a permission to view the type, **Node groups**, select the specific node this user role will have permissions to view.

    * Click **Add permission,** and then click the commit button.

* Add a user to a user role

    * In the list of user roles, click a user role that you want to add a user to.

    * Click the **Member users** tab.

    * In the **User name** field, from the drop-down list, select the user you want to add, click **Add user**, and then click the commit button.

#### Demonstrate knowledge of how to troubleshoot PE Console

[Finding Common Problems](https://docs.puppetlabs.com/pe/latest/trouble_console-db.html)

* Using cURL to troubleshoot classification info in the PE console

    * Determine what node groups the NC has and why data they contain

        *    curl https://$(hostname -f):4433/classifier-api/v1/groups > classifier_groups.json      --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem      --cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem      --key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem

    * Determine what data the NC will generate for a given node name

        * curl https://$(hostname -f):4433/classifier-api/v1/classified/nodes/<SOME NODE NAME> > node_classification.json      --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem      --cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem      --key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem

        * to get classification data for dynamically grouped nodes

            * curl https://$(hostname -f):4433/classifier-api/v1/groups > classifier_groups.json      --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem      --cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem      --key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem

* PostgreSQL is taking up too much space

    * PostgreSQL should have autovaccum=on by default

        * if you’re having memory issues from the database growing too large and unwieldy, make sure this setting did not get turned off

* PostgreSQL buffer memory causes PE install to fail

    * when installing PE on machines with large amounts of RAM, the PostgreSQL database will use more shared buffer memory than is available.

        * suggested workaround is tweak the machines shmmax and shmall kernel settings

            * sysctl -w kernel.shmmax=<your shmmax calculation>sysctl -w kernel.shmall=<your shmall calculation>

* PuppetDB’s default port conflicts

    * PuppetDB communicates over port 8081

        * workaround is installing with an answer file that specified a different port with q_puppetdb_port

* Recovering from a lost console admin password

    * utility script located in the installer tarball

* The console has too many pending tasks

    * the console either does not have enough worker processes

    * the worker processes have died and need to be restarted

    * pe-puppet-dashboard-workers restart

[The Log Blog: An Update on Debugging Puppet Enterprise](https://puppetlabs.com/blog/update-on-debugging-puppet-enterprise)

* Puppet server

    * /var/log/pe-puppetserver directory

        * puppetserver.log

            * what the master did during an agent run

            * compile errors

            * other errors

        * pe-puppetserver-daemon.log

            * where log messages go when the logback logging system is not running

        * puppetserver-access.log

            * HTTP traffic similar to Apache log format

    * uses JVM logback library for logging

* When something goes wrong with the console

    * /var/log/pe-console-services

        * console-services.log

            * main log for the pe-console-services

                * runs all of the console on Jetty

                * all console requests go through this service

                * uses the JVM Logback library

        * pe-console-services-daemon.log

            * similar to the puppetserver-daemon.log, this contains errors that happen while the pe-console-services service is starting

        * console-services-access.log

            * all HTTP requests made to the console

    * /var/log/pe-httpd/error.log and /var/log/pe-puppet-dashboard/production.log

        * problems with portions of the console other than Node Manager, access control, and authentication and authorization

    * /var/log/pe-httpd/puppet-dashboard/access.log

        * HTTP requests that reach the Ruby/Apache layer of the console

* Problems with Logging into the console

    * /var/log/pe-console-services/console-services.log

* Live Management and MCollective

    * /var/log/pe-httpd/error.log

    * /var/log/pe-puppet-dashboard/live-management.log

#### Describe reporting capabilities in PE Console

[Viewing Reports and Inventory Data](https://docs.puppetlabs.com/pe/latest/console_reports.html)

* Infrastructure reports

    * every Puppet run generates a report that provides information on that run

    * each report lists the number of resources on the node in each of the following states

        * **Failed**: Number of resources that failed.

        * **Changed**: Number of resources that changed.

        * **Unchanged**: Number of resources that remained unchanged.

        * **No-op**: Number of resources that would have been changed if not run in the no-op mode.

        * **Skipped**: Number of resources that were skipped because they depended on resources that failed.

        * **Failed** **restarts**: Number of resources that were supposed to restart but didn’t. For example, if changes to a resource notify a separate resource to restart, and that resource doesn’t restart, a failed restart is reported. It’s an indirect failure that occurred in a resource that was otherwise unchanged.

    * each reports also contains

        * **Config retrieval:** Time spent retrieving the catalog for the node (in seconds).

        * **Run time**: Time spent applying the catalog on the node (in seconds).

### Ecosystem

#### Describe the purpose of PuppetDB

[PuppetDB Overview](http://docs.puppetlabs.com/puppetdb/2.2/)

* PuppetDB stores

    * most recent facts from every node

    * most recent catalog for every node

    * Optionally, 14 days of event reports

* inventory of metadata that is searchable

#### Demonstrate knowledge of Hiera

[Hiera 1: Overview](https://docs.puppet.com/hiera/3.2/)

* Overview

    * key/value lookup tool for configuration data, built to make Puppet better and let you set node specific data without repeating yourself

* Getting started

    * Install hiera

    * make a hiera.yaml

    * arrange a hierarchy that fits your site/data

    * write data sources

    * use your hiera data in Puppet

* Why Hiera?

    * Making Puppet better

        * keeps site-specific data out of your manifest

            * classes can request whatever data they need, and your Hiera data will act like a site-wide config file

            * this makes it easier to:

                * configure your own nodes

                * re-use public Puppet modules

                * publish your own modules for collaboration

    * Avoiding repetition

        * write common data for most nodes

        * override some values for machines

        * override values for one or two unique nodes

    * Hiera uses a configurable hierarchy when choosing data sources 

[Hiera 1: installation](http://docs.puppetlabs.com/hiera/1/installing.html)

* Prerequisites

    * *nix and Windows systems

    * requires Ruby 1.8.5 or later

    * requires Puppet 2.7.X or later

        * if used with 2.7.X it requires the additional hiera-puppet package

* Installing

    * should be installed on the master server

    * step 1: install the hiera package or gem

        * sudo puppet resource package hiera ensure=installed

            * or sudo gem install hiera

    * step 2: install the puppet functions if using Puppet 2.7.X

        * sudo puppet resource package hiera-puppet ensure=installed

            * or sudo gem install hiera-puppet

[Configuration and hiera.yaml](https://docs.puppet.com/hiera/3.2/configuring.html)

* The hiera.yaml config file

    * location

        * /etc/puppetlabs/puppet/hiera.yaml on *nix systems

        * COMMON_APPDATA\PuppetLabs\puppet\hiera.yaml on Windows

        * can be changed with the hiera_config setting in puppet.conf

    * format

        * must be a YAML hash

        * each top level key in the hash **must be a Ruby symbol with a colon (:) prefix**

        * example config

            * ---:backends:  - yaml  - json:yaml:  :datadir: "/etc/puppetlabs/code/environments/%{::environment}/hieradata":json:  :datadir: "/etc/puppetlabs/code/environments/%{::environment}/hieradata":hierarchy:  - "nodes/%{::trusted.certname}"  - "virtual/%{::virtual}"  - "common"

    * global settings

        * hierarchy

            * string or array of strings

            * name of a static or dynamic data source

            * sources are checked from top to bottom

        * backends

            * name of an available Hiera backend

            * built in backends are yaml and json

        * logger

            * available logger

            * built in loggers are 

                * console

                * puppet

                * noop

        * merge_behavior

            * native

                * merge top-level keys only

            * deep (requires deep_merge ruby gem)

                * merge recursively

                    * in the event of conflicting keys, allow lower priority values to win

                    * **pretty much never want this**

            * deeper (requires deep_merge ruby gem)

                * merge recursively

                    * in the event of conflicting keys, allow higher priority values to win

[Hierarchies](https://docs.puppet.com/hiera/3.2/hierarchy.html)

* Creating hierarchies

    * hierarchy should be an array

    * location and syntax

        * example hiera.yaml

            * **# /etc/puppetlabs/puppet/hiera.yaml********---********:****hierarchy****:****  ****-**** ****"nodes/%{trusted.certname}"****  ****-**** ****"environment/%{server_facts.environment}"****  ****-**** ****"virtual/%{::is_virtual}"****  ****-**** common**

* terminology

    * static data source

        * hierarchy element without any interpolation tokens

        * in the example above, common is the static 

    * dynamic data source

        * element with at least one interpolation token	

* behavior

    * Ordering

        * if a data source in the hierarchy doesn’t exist, Hiera will move on to the next source

        * if a data source exists but does not have the piece of data Hiera is searching for, it will move on

        * if value is found

            * in a normal (priority) lookup, Hiera will stop

            * in an array lookup, Hiera will continue, then return all of the discovered values as a flattened array

            * in a hash lookup, Hiera will continue, expecting every value to be a hash and then will merge all of the discovered hashes

        * example data source resolution

            * $trusted[certname] = web01.example.com

            * $server_facts[environment] = production

            * $::is_virtual = true

![image alt text](image_2.png)

[Lookup Types](https://docs.puppet.com/hiera/3.2/lookup_types.html)

* Hiera always takes a lookup key and returns a single value

* Priority (default)

    * priority lookup gets a value from the most specific matching level of the hierarchy

    * first match

    * default lookup method

    * lookup keys

        * top-level lookup keys

            * returns the entire value of the key

        * qualified keys

            * return just the specified part of a value

        * $ hiera user{"name"=>"kim", "home"=>"/home/kim"}$ hiera user.namekim

* Array merge

    * assembles a value from every matching level of the hierarchy

    * flattens them into a single array of unique values

        * example

            * web01.example.com- common

            * # web01.example.com.yamlmykey: one# common.yamlmykey:  - two  - three

* Hash merge

    * assembles a value from every matching level of the hierarchy

    * merges the hashes into a single hash

    * native merging

        * hiera merges only the top-level keys and values

            * - web01.example.com- common

            * # web01.example.com.yamlmykey:  z: "local value"# common.yamlmykey:  a: "common value"  b: "other common value"  z: "default local value"

            * returns 

                * {z => "local value", a => "common value", b => "other common value"}

    * deep and deeper merging is also possible

[Writing Data Sources](https://docs.puppetlabs.com/hiera/1/data_sources.html)

* YAML

    * data sources on disk, in a directory specified in its :datadir setting

    * data format

        * standard YAML mapping(hash)

        * example

            * ---# arrayapache-packages:    - apache2    - apache2-common    - apache2-utils# stringapache-service: apache2# interpolated facter variablehosts_entry: "sandbox.%{::fqdn}"# hashsshd_settings:    root_allowed: "no"    password_allowed: "yes"# alternate hash notationsshd_settings: {root_allowed: "no", password_allowed: "yes"}# to return "true" or "false"sshd_settings: {root_allowed: no, password_allowed: yes}

* JSON

    * data sources on disk, in a directory specified in its :datadir setting

    * data format

        * example

            * {    "apache-packages" : [    "apache2",    "apache2-common",    "apache2-utils"    ],    "hosts_entry" :  "sandbox.%{fqdn}",    "sshd_settings" : {                        "root_allowed" : "no",                        "password_allowed" : "no"                      }}

[Interpolation Tokens, Variables and Lookup Functions](https://docs.puppet.com/hiera/3.2/variables.html)

* Interpolation tokens

    * Look like %{variable}, %{variable.subkey}, or %{function("input")}

    * interpolating normal variables

        * example

            * smtpserver: "mail.%{::domain}

    * interpolating hash or array elements

        * if a variable is an array or a hash, you can interpolate a single element of it by putting a dot and a subkey after the variable name

    * using lookup functions

        * scope()

            * syntax 

                * smtpserver: "mail.%{::domain}"smtpserver: "mail.%{scope('::domain')}"

        * hiera()

            * performs a Hiera lookup, using its input as the lookup key

            * syntax

                * wordpress::database_server: "%{hiera('instances::mysql::public_hostname')}"

[Usage with Puppet](https://docs.puppet.com/hiera/3.2/puppet.html)

* Enabling and configuring Hiera for Puppet

    * Puppet 4

        * ships with Hiera support already enabled

            * expects to find the hiera.yaml file at /etc/puppetlabs/puppet/hiera.yaml

    * Puppet variables passed to Hiera

        * from Puppet

            * whenever a Hiera lookup is triggered

                * Hiera receives a copy of all of the variables currently available to Puppet, including top scope and local scope	

        * Special pseudo-variables

            * calling_module - module in which the lookup is written

            * calling_class - class in which the lookup is evaluated

            * calling_class_path - name of the class with :: replaced by /

        * Best practices

            * two practices we always recommend when using Puppet’s variables

                * do not use local Puppet variables in hierarchy or data sources

                    * only use facts and top-scope variables set by a node classifier

                * use absolute top-scope notation ie (%{::is_virtual})

    * Automatic parameter lookup

        * automatically retrieves class parameters from Hiera using keys like myclass::param_one

        * example

            *  In this example, $parameter's value gets set when `myclass` is eventually declared.# Class definition:class myclass ($parameter_one = "default text") {  file {'/tmp/foo':    ensure  => file,    content => $parameter_one,  }}

* Hiera lookup functions

    * hiera

        * standard priority lookup

    * hiera_array

        * array merge lookup

            * gets all the string or array values in the hierarchy for a given key, then flattens them into a single array of unique values

    * hiera_hash

        * expects every value in the hierarchy for a given key to be a hash, and merges the top-level keys in each ash into a single hash

    * Don’t use the lookup functions from templates

    * example

        * # /etc/puppetlabs/code/production/hieradata/appservers.yaml---proxies:  - hostname: lb01.example.com    ipaddress: 192.168.22.21  - hostname: lb02.example.com    ipaddress: 192.168.22.28

        * # Get the structured data:$proxies = hiera('proxies')# Index into the structure:$use_ip = $proxies[1]['ipaddress'] # will be 192.168.22.28

        * # get only what you need from Hiera$use_ip = hiera( 'proxies.1.ipaddress' )

[Complete Example](https://docs.puppet.com/hiera/3.2/complete_example.html)

* What can you do with Hiera?

    * NTP is a good example

        * using hiera_include

            * in site.pp

                * hiera_include('classes')

            * in hostname.yaml

                * ---classes:  - ntp  - apache  - postfixntp::restrict: -ntp::autoupdate: **false**ntp::enable: **true**ntp::servers:  - 0.us.pool.ntp.org iburst  - 1.us.pool.ntp.org iburst  - 2.us.pool.ntp.org iburst  - 3.us.pool.ntp.org iburst

[Usage on the Commandline](https://docs.puppetlabs.com/hiera/1/command_line.html)

* Command line usage

    * Hiera provides a command line tool that’s useful for verifying that your hierarchy is constructed correctly and that your data sources are returning the values you expect.

    * Typically run from the Puppet master

    * invocation

        * hiera key_to_lookup (ie. hiera ntp_server)

    * MCollective

        * you can ask any node running MCollective to send you its facts

        * hiera ntp_server -m balancer01.example.com

        * pe_enterprise

            * # sudo -iu peadmin$ hiera ntp_server -m balancer01.example.com

[Writing New Backends](https://docs.puppet.com/hiera/3.2/custom_backends.html)

* Complete example

    * class Hiera  module Backend    class File_backend      def initialize        Hiera.debug("Hiera File backend starting")      end      def lookup(key, scope, order_override, resolution_type, context)        answer = nil        Hiera.debug("Looking up #{key} in File backend")        Backend.datasources(scope, order_override) do |source|          Hiera.debug("Looking for data source #{source}")          file = Backend.datafile(:file, scope, source, "d") or next          path = File.join(file, key)          next unless File.exist?(path)          data = File.read(path)          next unless data          break if answer = Backend.parse_answer(data, scope, extra_data, context)        end        return answer      end    end  endend

#### Describe the usage of MCollective

[Mcollective Collective](http://docs.puppetlabs.com/mcollective/)

* Marionette collective

    * framework for building server orchestration or parallel job-execution systems

    * unique strengths for working on large number of servers

        * instead of relying on a static list of hosts to command it, it uses metadata-based discovery and filtering

        * It can use data sources like PuppetDB

        * real-time discovery across the network

        * it uses publish/subscribe middleware to communicate in parallel with many hosts at once

* What is MCollective, and what does it allow you to do?

    * interact with clusters of servers

    * use a broadcast paradigm to distribute requests

    * break free from identifying devices through complex host-naming conventions, and instead use metadata from Puppet, Facter, or other sources to address them

    * custom-reports about your infrastructure

    * agent plugins to manage packages, services, and other common components

    * simple RPC style agents, clients, and web UIs in Ruby

    * re-use middleware features for clustering, routing, and network isolation

* Pluggable core

    * stable core framework that you can build into a system to meet your own needs

        * replace our STOMP-compliant middleware with your own

        * replace our authorization system with one that suits your local needs

        * replace our Ruby Marshal and YAML-based serialization with your own such as JSON

        * ADd data sources

        * Create a central inventory of services using MCollective as transport

[Using Mcollective Command Line Applications](http://docs.puppetlabs.com/mcollective/reference/basic/basic_cli_usage.html)

* Basic usage of the **bso **command

    * example

        * % mco pingdev8                                     time=126.19 msdev6                                     time=132.79 msdev10                                    time=133.57 ms

    * mco help for full list of applications

* Making RPC requests

    * Overview of a request

        * the rpc application is the main application used to make requests

        * example

            * % mco rpc service start service=httpdDetermining the amount of hosts matching filter for 2 seconds .... 10

        * order of events

            * perform discovery against the network and discover the 10 servers

            * send the request and then show a progress bar of the replies

            * show any results that were out of the ordinary 

            * show some statistics

    * Anatomy of a request

        * mco rpc service stop service=httpd

            * using the rpc application - a generic application that can interact with any agent

            * directing our request to machines with the service agent

            * sending a request to the stop action of the service agent

            * supplying a value, httpd, to the service argument of the stop action

        * mco rpc --agent service --action stop --argument service=httpd

    * Discovering available agents

        * mco plugin doc

    * Filtering

        * example

            * # all machines with the service agent% mco ping -A service% mco ping --with-agent service# all machines with the apache class on them% mco ping -C apache% mco ping --with-class apache# all machines with a class that match the regular expression% mco ping -C /service/# all machines in the UK% mco ping -F country=uk% mco ping --with-fact country=uk# all machines in either UK or USA% mco ping -F "country=/uk|us/"# just the machines called dev1 or dev2% mco ping -I dev1 -I dev2# all machines in the domain foo.com% mco ping -I /foo.com$/

#### Demonstrate knowledge of Facter

[Facter 3.4](https://docs.puppet.com/facter/3.4/)

* Refer to web doc for Facter outline/index

[Core Facts](http://docs.puppetlabs.com/facter/2.2/core_facts.html)

* Refer to web doc for full list

[Custom Fact Overview/Reference](https://docs.puppet.com/facter/3.4/fact_overview.html)

* Overview of a Facter Fact

    * simple assemblage of just a few different elements

    * **fact** is a piece of information about a given node

    * **resolution** is a way of obtaining that information from the system

        * every fact needs to have at least one resolution

* Writing facts with simple resolutions

    * most facts are resolved all at once

    * both flat and structured facts can have simple resolutions

    * example that relies on a single shell command

        * Facter.add(:rubypath) do  setcode 'which ruby'end

    * different resolutions for different operating systems

        * Facter.add(:rubypath) do  setcode 'which ruby'endFacter.add(:rubypath) do  confine :osfamily => "Windows"  # Windows uses 'where' instead of 'which'  setcode 'where ruby'end

    * main components of simple resolutions

        * A call to Facter.add(:fact_name):

            * This introduces a new fact or a new resolution for an existing fact with the same name.

            * The name can be either a symbol or a string.

            * You can optionally pass :type => :simple as a parameter, but it will have no effect since that’s already the default.

            * The rest of the fact is wrapped in the add call’s do ... end block.

        * Zero or more confine statements:

            * These determine whether the resolution is suitable (and therefore will be evaluated).

            * They can either match against the value of another fact or evaluate a Ruby block.

            * If given a symbol or string representing a fact name, a block is required and the block will receive the fact’s value as an argument.

            * If given a hash, the keys are expected to be fact names. The values of the hash are either the expected fact values or an array of values to compare against.

            * If given a block, the confine is suitable if the block returns a value other than nil or false.

        * An optional has_weight statement:

            * When multiple resolutions are available for a fact, resolutions will be evaluated from highest weight value to lowest.

            * It must be an integer greater than 0.

            * The weight defaults to the number of confine statements for the resolution.

        * A setcode statement that determines the value of the fact:

            * It can take either a string or a block.

            * If given a string, Facter will execute it as a shell command. If the command succeeds, the output of the command will be the value of the fact. If the command fails, the next suitable resolution will be evaluated.

            * If given a block, the block’s return value will be the value of the fact unless the block returns nil. If nil is returned, the next suitable resolution will be evaluated.

            * To execute shell commands within a setcode block, use the Facter::Core::Execution.exec function.

            * If multiple setcode statements are evaluated for a single resolution, only the last setcode block will be used.

* Writing structured facts

    * take the form of hashes or arrays

    * example

        * Facter.add(:interfaces_hash) do  setcode do    interfaces_hash = {}    Facter.value(:interfaces_array).each do |interface|      ipaddress = Facter.value("ipaddress_#{interface}")      if ipaddress        interfaces_hash[interface] = ipaddress      end    end    interfaces_hash  endend

    * main components of aggregate resolutions

        * A call to Facter.add(:fact_name, :type => :aggregate):

            * This introduces a new fact or a new resolution for an existing fact with the same name.

            * The name can be either a symbol or a string.

            * The :type => :aggregate parameter is required for aggregate resolutions.

            * The rest of the fact is wrapped in the add call’s do ... end block.

        * Zero or more confine statements:

            * These determine whether the resolution is suitable and (therefore will be evaluated).

            * They can either match against the value of another fact or evaluate a Ruby block.

            * If given a symbol or string representing a fact name, a block is required and the block will receive the fact’s value as an argument.

            * If given a hash, the keys are expected to be fact names. The values of the hash are either the expected fact values or an array of values to compare against.

            * If given a block, the confine is suitable if the block returns a value other than nil or false.

        * An optional has_weight statement:

            * When multiple resolutions are available for a fact, resolutions will be evaluated from highest weight value to lowest.

            * It must be an integer greater than 0.

            * The weight defaults to the number of confine statements for the resolution.

        * One or more calls to chunk, each containing:

            * A name (as the argument to chunk).

            * A block of code, which is responsible for resolving the chunk to a value. The block’s return value will be the value of the chunk; it can be any type, but is typically a hash or array.

        * An optional aggregate block:

            * If this is absent, Facter will automatically merge hashes with hashes or arrays with arrays.

            * If you want to merge the chunks in any other way, you’ll need to make a call to aggregate, which takes a block of code.

            * The block is passed one argument (chunks, in the example), which is a hash of chunk name to chunk value for all the chunks in the resolution.

[Custom Fact Walkthrough](https://docs.puppet.com/facter/3.4/custom_facts.html#loading-custom-facts)

* Custom facts

    * extend facter by writing your own custom facts

* Adding custom facts to Facter

    * you can add new facts by writing snippets of Ruby code on the Puppet master

    * Puppet then uses Plugins in modules to distribute facts to the clients

        * automatically enabled when you install the module that contains them

<table>
  <tr>
    <td>Plugin type</td>
    <td>Module subdirectory</td>
    <td>Mount point</td>
    <td>Agent directory</td>
  </tr>
  <tr>
    <td>External facts</td>
    <td><MODULE>/facts.d</td>
    <td>pluginfacts</td>
    <td><VARDIR>/facts.d</td>
  </tr>
  <tr>
    <td>Ruby plugins</td>
    <td><MODULE>/lib</td>
    <td>plugins</td>
    <td><VARDIR>/lib</td>
  </tr>
</table>


To add plugins to a module, put them in the following directories:

<table>
  <tr>
    <td>Type of plugin</td>
    <td>Module subdirectory</td>
  </tr>
  <tr>
    <td>Facts</td>
    <td>lib/facter</td>
  </tr>
  <tr>
    <td>Functions (Ruby, modern Puppet::Functions API)</td>
    <td>lib/puppet/functions</td>
  </tr>
  <tr>
    <td>Functions (Ruby, legacy Puppet::Parser::Functions API)</td>
    <td>lib/puppet/parser/functions</td>
  </tr>
  <tr>
    <td>Functions (Puppet language)</td>
    <td>functions</td>
  </tr>
  <tr>
    <td>Resource types</td>
    <td>lib/puppet/type</td>
  </tr>
  <tr>
    <td>Resource providers</td>
    <td>lib/puppet/provider</td>
  </tr>
  <tr>
    <td>External facts</td>
    <td>facts.d</td>
  </tr>
  <tr>
    <td>Augeas lenses</td>
    <td>lib/augeas/lenses</td>
  </tr>
</table>


* Loading custom facts

    * $LOAD\_PATH or the Ruby library load path

    * --custom-dir cli option

    * the environment variable ‘FACTERLIB’

[External Facts](https://docs.puppet.com/facter/3.4/custom_facts.html#external-facts)

* External facts overview

    * What are external facts?

        * External facts provide a way to use arbitrary executables or scripts as facts

    * Fact locations

        * best way to distrubte external facts is with pluginsync, which added support for them in Puppet 3.4/Facter 2.0.1

        * to add external facts to your modules, just place them in **<MODULEPATH>/<MODULE>/facts.d/**

        * on Unix/Linux/OS X

            * /opt/puppetlabs/facter/facts.d/

            * /etc/puppetlabs/facter/facts.d/

            * /etc/facter/facts.d/

        * on Windows

            * C:\ProgramData\PuppetLabs\facter\facts.d\

        * on Windows 2003

            * C:\Documents and Settings\All Users\Application Data\PuppetLabs\facter\facts.d\

    * for facter to parse the output, the script must return key/value pairs on STDOUT in the format of key1=value1

* Drawbacks

    * An external fact cannot internally reference another fact

    * external executable facts are forked instead of executed within the same process

    * distributing executable facts through pluginsync requires Puppet 3.4.0

