---
layout: center
image: /grid_start.jpg
backgroundSize: contain
---
# Quick practice before it's your time to shine!
How can you add a Team filter for the F1 drivers' grid?

```php
  ->addFilter(Filter::create('teamName', TeamFilter::class)) 
```

<v-clicks>

* ```php
    #[AsFilter(formType: TeamFilterType::class,  template: FilterTemplate::SELECT->value,)]
    final class TeamFilter
* ```php
    use Sylius\Component\Grid\Filtering\ConfigurableFilterInterface;

    final class TeamFilter implements ConfigurableFilterInterface {
  
    public static function getFormType(): string
    {
        return TeamFilterType::class;
    }
    // ...
* ```php
  use Sylius\Component\Grid\Filtering\FilterInterface;
  
  #[AsFilter(formType: TeamFilterType::class, template: FilterTemplate::SELECT->value,)]
  final class TeamFilter implements FilterInterface

  
</v-clicks>


<!--
    Answers 2 and 3 are correct
-->

---

Find the odd one out.

<v-clicks>

* ```#[AsFilter()] ```
* ```#[AsGrid()]```
* <span v-mark="{ at: 5, color: 'red', type: 'circle' }">```#[AsProvider()]```</span>
* ```#[AsResource()]```

  
</v-clicks>

---

What's wrong with this code?


```php
    use Sylius\Component\Grid\Filtering\ConfigurableFilterInterface;

    #[AsFilter(formType: TeamFilterType::class,  template: FilterTemplate::SELECT->value,)]
    final class TeamFilter implements ConfigurableFilterInterface {
```

<!--
    @TODO replace this question with something more pertinent and interesting than just the wrong interface is implemented...
-->