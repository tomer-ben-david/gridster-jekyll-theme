---
layout: post
title:  "Puppet cheasheet"
date:   2017-01-10 22:18:00
categories: cheatsheet,puppet,devops
comments: true
---
**simple puppet manifest**

```ruby
node 'yourhostname' {
    file { '/test.txt':
        ensure => 'present',
        content => inline_template("this is a test <%= Time.now %>\n"),
    }
}
```

**print**

```ruby
class someclass {
  ...
  notify {'test test':}
}

node 'mynode' {
    class { '::printosfamily': }
}

class printosfamily {
    notify { 'OsFamilyTest':
        withpath => true,
        name => "my osFamily is ${::osfamily}",
     }
}
```

**invoke (run) a class**

```class { '::apache': }```

* the above command runs the puppet class 'apache'.
* `::` at the beginning of `apache` means that we are looking for a `top level` apache, meaning not an apache not defined as top level class inside another module. 

**condition `if`**

```ruby
if $osfamily == 'redhat' {
    package { 'php-xml':
        ensure => 'present',
    }
}
```

**ordering**

`File['/var/www/html/index.html'] -> Vcsrepo['/var/www/html']`

execute first the `index.html` resoure and only then the `/var/www/html`.
note the uppercase in `File` and `Vcsrepo`

**install a module maintained by 'puppet labs'**

on puppet master:

`sudo puppet module install puppetlabs-apache --modulepath /etc/puppet/environments/production/modules`

**Puppet ssl keys dir**

```/var/lib/puppet/ssl```

**Restore or get history file after puppet change**

```sudo puppet filebucket -l --bucket /var/lib/puppet/clientbucket restore /info.txt c62hfejd67kjsdhfksjhfkjh276```
```sudo puppet filebucket -l --bucket /var/lib/puppet/clientbucket get /info.txt c62hfejd67kjsdhfksjhfkjh276```

* ```/var/lib/puppet/clientbucket```: where the local pre change changes are stored.

**Variable based on osfamily**

```ruby
$ntpservice = $osfamily ? {
    'redhat' => 'ntpd',
    'debian' => 'ntp',
    default => 'ntp',
}
```

then to use:

```ruby
service { $ntpservice:
    ensure => 'running',
    enabled => true,
}
```

**Reuse code - `classes`**

define class:

```ruby
class linux {
    package { 'ntp': {
        ensure => 'installed',
    }
} 
```

use class:

```ruby
node 'wiki': {
    { class 'linux': }
}
```

**run same command for array**

```ruby
class linux {
    $toinstall = ['git', 'ntp', 'svn']
    
    package { $admintools: {
            ensure => 'installed', # will install all in array.
        }    
    }
}
```

**Generate puppet module**

```ruby
cd /etc/puppet/environmentsproduction/modules
sudo puppet module generate myname-mymodulename --environment production
mv myname-mymodulename mymodulename # due to puppet limitations and bugs.
vi manifests/init.pp
this contains mymodulename class definition that we previously defined in nodes.pp
```

node the `myname-` is needed for module uniqueness if we upload it to repository.



