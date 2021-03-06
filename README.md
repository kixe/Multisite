# Multisite module for ProcessWire
Complete rewrite of Multisite Module.  (_"not much left from the original module and fork, but the basic idea is the same"_).

The basic module was written by Antti Peisa and forked by Philipp "Soma" Urlich

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
- FULL multilanguage support
- take in account `$template->slashUrl` settings
- take in account `LanguageSupportPageNames::useHomeSegment` settings
- Fallback to default 404 page if custom 404 is not set
- Throws exception in case of misconfiguration
- Access of pages residing inside a MultisitePageTree for other domains other than the corresponding domain is disallowed. (404)
- Pages inside a MultisitePageTree accessible only via url based on the corresponding domain. 404 instead of unexpected redirects.
- Page paths are set via hook in `Page::path()`  and `LanguageSupportPageNames::getPagePath()` instead of modifying the return of `Page::render()`
- Crosslink support via the RTE (CK Editor) Link plugin
- Page view links in admin with correctly modified urls. For the branch related to calling domain only.
- You can place your root branch elsewhere, no need to place at the first child level under home.
- Support of redirects
  
## How to use it?
+ Install Module
+ Each Domain you use must point to your ProcessWire Installation
+ Setup config.php

### Setup MultisiteDomains in config.php
```php
$config->MultisiteDomains = array(
    "example.com" => array( // domain name is used to map to root page
            "root" => 1027, // page id
        ),
    "example.org" => array(
            "root" => 1028,
            "http404" => 1029 // custom 404 page
        )
		 "example.de" => array(
            "root" => "https://example.org" // redirect
        )
);
```


### Requirements Multilanguage
+ LanguageSupportPageNames must be installed
+ LanguageSupportPageNames::getPagePath() MUST BE MODIFIED hookable in core. Currently LanguageSupportPageNames::getPagePath() is not hookable. This core function must be changed to  hookable otherwise language support doesn't work. Please support this feature request: [https://github.com/processwire/processwire-requests/issues/94](https://github.com/processwire/processwire-requests/issues/94)
+ a page name for the default language and any other language must be set for the superRoot page ($page->id = 1)

### merge httpHosts in config.php
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

### Modified API Vars
The following API Vars are modified by the module.  

+ `$page->url`
+ `$page->httpUrl`
+ `$page->path`
+ `$page->localPath()`
+ `$page->localUrl()`
+ `$page->localHttpUrl()`
   
### Important
This module doesn't provide new API vars and your templates doesn't know about this module (except the modified API vars). You have to use the ProcessWire API in the same way as in a single site environment.


