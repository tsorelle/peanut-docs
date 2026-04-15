# Peanut Overview

The following lists the main components of the Peanut framework.  Each has a related repository and can be combined 
with others for particular use cases. 

For more detailed documentation see the [Peanut-docs repository](https://github.com/tsorelle/peanut-docs/blob/master/documentation/index.md).

## TOPS Core
PHP Library for building Peanut applications.  Primarily used for report service implementation, data access framework,
general system utilities.

### Reference Application Repository

[Tops-core](https://github.com/tsorelle/Tops-core) (public)

### Dependencies
- [PHP](https://www.php.net/) Programming language used for websites and general uses.

## Peanut Core
The core application of the Peanut framework. Used in particular implementations such as CMS modules and Nutshell Applications.
### Reference Application Repository
[Peanut-core](https://github.com/tsorelle/Tops-core) (public)

### Dependencies
- TOPS Core (see above)
- [TypeScript](https://www.typescriptlang.org/) The front end language used for the Peanut framework. Compiles to JavaScript.
- [HeadJs](https://github.com/headjs/headjs) a simple JavaScript loader.
- [jQuery/Ajax](http://jquery.com/) A JavaScript library for remote service calls extracted from the JQuery library.
- [KnockoutJS](https://knockoutjs.com/) A JavaScript library for data binding and view management.
- [Bootstrap](https://getbootstrap.com/) CSS/JS framework for responsive design. Not strictly required but used in most implementations.

## QNUT
A set of Peanut packages that are used to implement website features for Quaker Meetings and Organizations.

QNUT includes these features and more:
- Member directory
- Committee directory
- Document library
- Mailing lists
- Committee pages
- Calender integration with committee system and notifications.

### Dependencies
- Peanut Core (see above)

### Reference Application Repository

A public reference application is not available at this time.

Private repositories for production implementations include:
- austinquakers-2019 (for https://austinquakers.org/)
- scym2021 (for https://scym.org/)

## Host Frameworks
Peanut may be used with a CMS system such as Wordpress or ConcreteCMS by means of a specially designed modules 
(e.g. "package" in ConcreteCMS or "plugin" in Wordpress) and one or more accompanying "themes".
If a simple application framework is desired Nutshell may be used.

In the past, host frameworks have included: Wordpress (earlier version) Drupal 7 and 8, and Concrete5.
These have been retired. Below we list the current host frameworks, as well as "Peanut for Wordpress Gutenberg" 
currently in development.  We do not yet have public reference application repositories for the CMS frameworks 
but list private repositories for production implementations.

###  Nutshell

Nutshell is a simple application framework designed around Peanut. Unlike the CMS implementations,
management of a Nutshell based application would require an experienced developer.
 
#### Reference Application Repository
- [Peanut-core](https://github.com/tsorelle/Peanut-core) (public)
#### Production examples:
- [Terrys-songs.net](https://terrys-songs-net)
- [Quakercall.net](https://quakercall.net)


#### Dependencies
- Peanut Core (see above)

### Peanut for ConcreteCMS

Packages and themes for use with ConcreteCMS as a custom "Block".

#### Reference Application Repository
- [austinquakers-2019](https://github.com/tsorelle/austinquakers-2019) (private)

#### Production examples:
- [austinquakers.org](https://austinquakers.org)
- [scym.org](https://scym.org)

#### Dependencies
- Peanut Core (see above)
- [ConcreteCMS](https://www.concretecms.org/) A PHP CMS framework.


### Peanut for Wordpress Gutenberg

In development.

