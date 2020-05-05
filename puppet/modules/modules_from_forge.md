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
