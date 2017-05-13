# Multisite module for ProcessWire
Complete rewrite of Multisite Module.
The basic module was written by Antti Peisa and forked by Philipp "Soma" Urlich (_"not much left from the original module and fork, but the basic concept is the same"_).

* [Github: Multisite](https://github.com/kixe/Multisite) by Christoph Thelen aka @kixe  
* [Github: Multisite](https://github.com/somatonic/Multisite) by Philipp "Soma" Urlich
* [Github: Multisite](https://github.com/apeisa/Multisite) by Antti Peisa – **deprecated**
* [ProcessWire Forum](https://processwire.com/talk/topic/1025-multisite/)
[ProcessWire Forum](https://processwire.com/talk/topic/1025-multisite/)

## What does this module do?
It allows you to run multiple sites with different domains from a single install, using the same database, same site folder, same modules, templates and core settings.

## Isn't there multisite support in core?
Yes, [kind of](https://processwire.com/api/modules/multi-site-support/). It allows you to run multiple pw-installations with shared core files (/wire/ folder) but with site folders (templates, custom modules) and databases for each installation.

## What is different to the initial versions of this module?
- Do not worry about page naming. The root page is defined via ID.
- Full multilanguage support
- take in account`$template->slashUrl` settings
- Fallback to default 404 page if not set
- Throws exception in case of misconfiguration
- Access of pages residing in a MultisitePageTree for other domains other than the corresponding root page is disallowed except for superuser
- Pages inside a MultisitePageTree accessible only via url based on to the corresponding domain (404 instead of unexpected redirects)
- Page paths are set via hook in Page::path() instead of modifying the return of Page::render()
- Crosslink support via the RTE (CK Editor) Link plugin
- Page view links in admin with correctly modified urls
  
## How to use it?
Just create pages under the root page and add these in the `config.php` by setting `$config->MultisiteDomains`.

### Add MultisiteDomains
```php
$config->MultisiteDomains = array(
    "example.com" => array( // domain name is used to map to root page
            "root" => 1027, // page id
        ),
    "example.org" => array(
            "root" => 1028,
            "http404" => 1029 // custom 404 page
        )
);
```


### Requirements  
+ All multisite root pages must be child pages of the original root page*
<small>*Page with id=1</small>

### httpHosts setup
```php
/**
 * HTTP Hosts Whitelist
 * 
 */
 $config->httpHosts = array('nomultisite.com');

/**
 * Multisite domain settings
 * 
 */
 $config->MultisiteDomains = […];
 
/**
 * Merge Multisitedomains in $config->httpHosts to set the correct $config->httpHost
 * 
 */
$config->httpHosts = array_unique(array_merge($config->httpHosts, array_keys($config->MultisiteDomains)));
```

### Domains
Make sure all domains points to the same server and directory.

### Good to know: some variables
```php
// get current domain
echo $modules->get('Multisite')->domain;
[dev]  'dev.domain1.com'
[live] 'domain1.com'

// get current site root
$siteRoot = $page->rootParent;
echo $siteRoot->name // root page name
[dev]  'www.domain1.com'
[live] 'www.domain1.com'

// use it as navigation starting point
foreach ($siteRoot->children as $child) {
  echo $child->title;
}
```

### TroubleShooting

#### Wrong page tree structure

```
- Homepage (main site home)
   - Example Page
   - 404 Page
   - Contact Page
   - www.domain2.com (a second site home)
     - 404 Page
   ...

```

But this wasn't ever recommended and it can lead to complications.

#### Domains are missing insite httpHosts
Check that all domains for the specific environment are included inside `$config->httpHosts`.

#### DNS Record is not set properly
The DNS Record of all domains must point to the same server.

#### Domains point to a wrong directory
All domains must point to the same directory.

#### Multi-language Domains are not working
Make sure the **LanguageSupportPageNames** module is installed.

### modified API Vars
+ `$page->url`
+ `$page->httpUrl`
+ `$page->path`
+ `$page->localPath()`
+ `$page->localUrl()`
+ `$page->localHttpUrl()`
   
### Important
This module doesn't provide new API vars and your templates doesn't know about this module (except the modified API vars). You have to use the ProcessWire API in the same way as in a single site environment.
  
## Please try out and let me know how it works!


