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

