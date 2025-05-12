# Sylius Grid Technical History
---
layout: quote
---

Paweł Jędrzejewski wrote

<v-clicks>

* Standalone Grid component, decoupled from persistence with a system of drivers.
* Standalone SyliusGridBundle, decoupled from SyliusResourceBundle.
* SyliusResourceBundle does not force you to use GridBundle.
* Doctrine ORM and Doctrine DBAL drivers for SyliusGridBundle, later I plan to have ElasticSearchDriver.
* Super easy to introduce new drivers, filters, columns and customize rendering of every single part;

</v-clicks>
