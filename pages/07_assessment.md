---
layout: center
image: /grid_start.jpg
backgroundSize: contain
---
# Quick practice before it's your time to shine!
How can you add a Team filter for the F1 drivers' grid using **AsFilter**?

```php
            ->addFilter(
                Filter::create('teamName', TeamFilter::class)
            )
```


<v-clicks>

* ```php
  #[AsFilter(formType: TeamFilterType::class, template: FilterTemplate::SELECT->value,)]
  final class TeamFilter implements FilterInterface
    
* ```php
    #[AsFilter(formType: TeamFilterType::class,  template: FilterTemplate::SELECT->value,)]
    final class TeamFilter
  
</v-clicks>