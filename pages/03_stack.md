---
layout: image
image: /stack_dashboard.png
backgroundSize: contain
hideInToc: true
---

---
layout: image-right
image: /mercedes_sketch.png
backgroundSize: contain
---

# What's under the hood of the Sylius Stack?

Create a lean & mean back-office in no time with :

<v-clicks>

* <span v-mark="{ at: 5, color: 'red', type: 'circle' }">**Sylius Grid bundle**</span>
* Sylius Resource bundle
* Doctrine ORM and Doctrine DBAL drivers for SyliusGridBundle 
* Bootstrap Admin UI
* Symfony UX, AssetMapper and more !

</v-clicks>

<!--
Sylius Stack is a set of autonomous tools/packages which, put together, let you build beautiful admin panels very quickly
* Standalone Grid component, decoupled from persistence =>  drivers 
    => the grid is driven by Doctrine ORM & DBAL drivers but now we have a Providers system which means we don't need Entities
* Standalone SyliusGridBundle, decoupled from SyliusResourceBundle
* SyliusResourceBundle does not force you to use GridBundle
* Doctrine ORM and Doctrine DBAL drivers for SyliusGridBundle
* Super easy to introduce new drivers, filters, columns and customize rendering of every single part;

-->