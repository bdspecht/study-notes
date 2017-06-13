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

