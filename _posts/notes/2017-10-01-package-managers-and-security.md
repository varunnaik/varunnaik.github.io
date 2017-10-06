---
layout: post
title: Package Managers and Security
tags: development security
---
### Package managers and security

There is a growing trend of extensions and packages being taken over by advertising companies and crackers.
When installing packages with tools such as **npm** take care that the package you are installing is legit.

  * [[https://twitter.com/o_cee/status/892306836199800836]]
  * [[http://www.drinchev.com/blog/alert-npm-modules-hijacked/]]
  * [[https://news.ycombinator.com/item?id=14902630]]

### Version Pinning and auto updates
It is unsafe to use package managers without explicit version pinning. Eg. v 1.2.3 precisely, not v 1.X.X or even 1.2.X. There have been cases of package dev's accounts being taken over maliciously by hackers who then release a point version that auto upgrades itself. __Auto updates of packages - even inadvertent ones -  are a very bad idea.__
It is important to use npm-shrinkwrap or such to freeze package versions - and it is very important to maintain this carefully.

  * [[https://a9t9.com/blog/chrome-extension-adware/]]
  * [[https://www.lucidchart.com/techblog/2017/03/15/package-management-stop-using-version-ranges/]]

### Typosquatting
This is a major problem, and great care must be taken when installing packages.
npm install reqiest instead of npm install request
can cause a typosquatted package to be installed which appears to work like the actual package but also has malicious code injected into it.

  * [[http://incolumitas.com/2016/06/08/typosquatting-package-managers/]]
  * [[https://news.ycombinator.com/item?id=14901566]]
  * [[https://arstechnica.com/information-technology/2017/09/devs-unknowingly-use-malicious-modules-put-into-official-python-repository/]]

### Obfuscated Code 
Having obfuscated code in a package is a significant red flag. Obfuscated (or binary) code in a package is reason enough to do without the package. Hidden code is fine in an end user app but it has no place in a dev package. Even if the obfuscated code is currently harmless the package could easily be sold tomorrow to a developer who then proceeds to add malicious code to it - code that is well hidden within the previously obfuscated code. Avoid.

### How packages become compromised
  * Typosquatting (cross-env vs crossenv)
  * Dev account gets phished (If the package also needs to be signed ala Maven then this is mitigated unless the dev's machien is also compromised)
  * Dev sells the package to an advertising company
  * Attackers inject code into the package without the dev catching on

### Security Updates
If you include a third party package in your application you are responsible for securing it. If the third party package includes more packages of its own, and one of those packages are compromised you are still responsible for the mess that occurrs.

The OWASP lists using packages with known vulnerabilities in the top ten ways applications are vulnerable/become compromised. [[https://www.owasp.org/images/f/f8/OWASP_Top_10_-_2013.pdf]]

In practise this means keeping an eye out for vulnerable packages and updating them as soon as you are aware. This is not necessarily related to package hijacks although it could be. The package could also have a vulnerability that exposes your application to problems.

The best option is to not use any packages you did not write.

A barebones ExpressJs app created with the Express creation tool has about 50 dependencies (including sub-dependencies). That's a massive attack surface.

**Therefore, if a hacker detects a vulnerability in one package every single webapp on the Internet that uses the package is now compromised. There is no such thing as safety in numbers although visibility will be higher for more popular packages meaning you are likely to be notified of hacks sooner - if those hacks are ever detected in the first place.** And remember: "But everyone else was doing it" is never a good defense. Ultimately if your app breaks it is your problem irrespective of the cause.

### Checking for vulnerabilities

There is no foolproof way apart from being generally aware.

[[https://github.com/nodesecurity/nsp]] audits your node packages for you. [[https://github.com/rubysec/bundler-audit]] is an equivalent for Ruby. Ruby generally has better support.

[[https://libraries.io/]] tracks package library versions.
 
### Local package cache
After pinning specific package versions, it is a good idea to keep a cache of the packages on your server. This is needed for a few reasons:

  - A left-pad debacle ([[http://left-pad.io/]]) like scenario, which leaves your packages unable to compile
  - It's 3 years into the future and the pinned version is obsolete and removed
  - Native build tools that a package needs are themselves obsolete
  - Reduces chances of being hacked if the npm https cert is stolen or if there are targeted MITM attacks
Note that this is hard to do because:
  - To do right we may need to store Mac, Windows, Linux versions of the packages
  - Native build tools, if any - __and their dependencies__ - must be cached as well
  - If upgrading a package the cache __and its dependencies__ must be updated as well
  - The package may not be compatible with newer versions of the engine (eg, the npm package may not be compatible with the newest nodejs engine)
  - It is 3 years in the future and while the cache is fine it is not possible to install a new package, x, because the old version of x is gone and the new version of x is not compatible with the packages in cache

### Conclusion
Using npm, pip, et. al. comes at a significant cost in terms of maintenance and upkeep. Maintainers, developers, and devops need to be constantly on the lookout for phishing and hacking attempts - a lack of discipline will cause problems. This is particularly difficult because things will work smoothly most of the time, and thus issues are likely to be ignored during routine development, maintenance, and testing - because there are no immediate errors/roadblocks. The cost of an error is extremely high (and could potentially be fatal, eg. like Wannacry) but likelihood is small.

