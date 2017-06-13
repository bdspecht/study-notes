#### **Identify Style Guide recommendations**

[The Puppet Language Style Guide](https://docs.puppetlabs.com/guides/style_guide.html)

* Spacing, indentation, and whitespace

    * Use two-space tabs

        * not literal tabs

    * No trailing whitespace

    * Trailing commas after all resource attributes and params

    * 140 linewidth

    * One empty line between resources, except for dependency chains

    * Aligned Hash rockets

* Quoting

    * Strings in single quotes, unless they contain variables or single quotes

    * All strings must be enclosed in braces when interpolated in a string

        * "${::operatingsystem} is not supported by ${module_name}"

    * Variables by themselves should not be quoted unless in a title

        * mode => $variable,

* Comments

    * Use #

    * Should explain why, not how

* Module metadata

    * Every publically available module must have metadata defined metadata.json file

* Dependencies

    * Hard dependencies must be declared in the metadata.json file. 

    * Soft dependencies in the README.md

        * Soft dependencies means a specific set of use cases

