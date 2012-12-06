# Modules

  - Self Contained and Distributable *recipes* for specific software or tasks
  
  - Used to manage with Puppet an application, system's resources, a local site or more complex structures
  
  - Modules must be placed in the Puppet Master modulepath
    
        puppet config print modulepath
        /etc/puppet/modules:/usr/share/puppet/modules
 
 
  - Puppet module tool to interface with Puppet Modules Forge
  
  
# How to Use

  - Mysql class is defined in $MODULE_PATH/mysql/manifests/init.pp

        class mysql (
          $required_param ,
          $optional_param = 'default' ,
          $lastparam      = 'default' 
          ) {

        # Comment
        # Here puppet code
        
          package { 'mysql': [...] }
 
          service { 'mysql': [...] }
        
          [...] Othet Puppet code to manage Mysql
        
        }
        
  - Once defined, a class can be included or declared        

        include mysql
        
        class { 'mysql':
          required_param  => 'my_value',
          $optional_parma => 'dont_like_defaults',
        }
        
   
# Paths of a module

        mysql/                     # Main module directory

        mysql/manifests/           # Manifests directory. Puppet Code here
        mysql/manifests/init.pp    # Main mysql class 
        mysql/manifests/module.pp  # A sample define: mysql::module

        mysql/lib/                 # Plugins directory. (Ruby code here) 
        
        mysql/templates/           # ERB Templates directory 
        
        mysql/files/               # Static files directory 

        mysql/spec/                # Puppet-rspec test 

        mysql/Modulefile           # Module's metadata descriptor


# Parametrized classes

# Files, files, files everywhere

  - Configuration files can be provided by Puppet in different ways:

  - Provide static files, sourced according to a defined path hierarchy

  - Provide erb templates based where you can place variables and code
  
  - Build files from fragments (Using custom types like concat )

  - Define files' single lines with **Augeas** or other inline editors
  

# Files provided as Static source files

        class autofs::client {
          file { "/etc/auto.homes":
            source => "puppet:///modules/autofs/auto.homes"
          }

  - Sourced files are searched in:

        $modulepath/autofs/files/auto.homes


# Erb templates

  - Files provisioned by Puppet can be ERB templates

  - Templates are referenced with the content attribute:

        content => template("resolver/resolv.conf.erb"),
  
    Which, on the puppet master is 
    
        $modulepath/resolver/templates/resolv.conf.erb   

  - In a template all the Puppet variables (facts or user assigned) can be used :

        # File managed by Puppet on <%= fqdn %>
        search <%= domain %>
        <% dns_servers.each do |ns| %>
        nameserver <%= ns %>
        <% end %> 



# Principes behind a Good and Reusable Module

  - Data Separation
    Configuration data is defined outside the module (or even Puppet manifests)
    Module’s behavior is managed via APIs
    Allow module’s extension and override via external data

  - Reusability
    Support different OS. Easily allow new additions.
    Customize behavior without changing module code
    Do not force author’s idea on how configurations should be provided

  - Standardization
    Follow PuppetLabs style guidelines (puppet-lint)
    Have coherent, predictable and intuitive interfaces
    Provide contextual documentation (puppet-doc)

  - Interoperability
    Limit cross-module dependencies
    Allow easy modules’ cherry picking 
    Be self contained, do not interfere with other modules’ resources

# What's a Good Module anyway?

  - The most reusable and customizable
  
  - The one full of features 

  - The one that works for you
  
  - The most essential, and optimized (but noe reusable) one
  
  - The quickest one to do now


# Modules documentation

  - Puppetdoc generates documentation from manifests comments:

        $ puppetdoc [--all] --mode rdoc [--outputdir ] [--debug|--verbose] [--trace]
           [--modulepath ] [--manifestdir ] [--config ]

  - Comment classes as below:

        # Class: apache
        #
        # This module manages Apache
        #
        # Parameters:
        #
        # Actions:
        #
        # Requires:
        #
        # [Remember: No empty lines between comments and class definition]
        class apache {
         	...
        }