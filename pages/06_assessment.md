---
layout: center
image: /grid_start.jpg
backgroundSize: contain
---

# Quick practice before it's your time to shine!
Q1: Find the odd one out.

<v-clicks>

* ```#[AsFilter] ```
* ```#[AsGrid]```
* <span v-mark="{ at: 5, color: 'red', type: 'circle' }">```#[AsProvider]```</span>
* ```#[AsResource]```


</v-clicks>

---

Q2: The OpenF1 API does not include a **/teams** endpoint, but you would like to create a grid listing all teams.
You've created this simple model ... : 

```php
final readonly class Team
{
    public function __construct(
        public string $id,
        public string $name,
        public string|null $color = null,
    ) {
    }
}
```

---

Q2: What command could you run to generate the missing grid?

<v-clicks>

* ```symfony console create:grid 'App\Model\Team'```
* ```symfony console make:resource 'App\Model\Team'```
* <span v-mark="{ at: 5, color: 'red', type: 'circle' }">```symfony console make:grid 'App\Model\Team'```</span>
* ```symfony console make:grid 'App\Resource\TeamResource'```

</v-clicks>

<!--
    Although technically, we still need to add a TeamResource to generate routing  
```php

#[AsResource(
    section: 'admin',
    templatesDir: '@SyliusAdminUi/crud',
    routePrefix: '/admin',
    operations: [
        new Index(grid: TeamGrid::class),
    ],
)]
final readonly class TeamResource implements ResourceInterface
{
    public function __construct(
        public string $id,
        public string $name,
        public string|null $color = null,
    ) {
    }

    public function getId(): string
    {
        return $this->name;
    }
}
```
-->


---
layout: center  
hideInToc: true
---

# symfony console make:grid 'App\Model\Team'

<img src="/teams_grid.png">

---

Q2: How can you add a Team filter for the F1 drivers' grid?

```php
  ->addFilter(Filter::create('teamName', TeamFilter::class)) 
```

<v-clicks>

* ```php
  #[AsFilter(formType: TeamFilterType::class, template: '@SyliusBootstrapAdminUi/shared/grid/filter/select.html.twig',)]
  final class TeamFilter implements FilterInterface


* ```php
  #[AsGridFilter(formType: TeamFilterType::class, template: '@SyliusBootstrapAdminUi/shared/grid/filter/select.html.twig',)]
  final class TeamFilter implements FilterInterface


* ```php
  #[Filter(formType: TeamFilterType::class, template: '@SyliusBootstrapAdminUi/shared/grid/filter/select.html.twig',)]
  final class TeamFilter implements FilterInterface


</v-clicks>


<!--
    Answers 2 and 3 are correct but in our case we only have a model, no resource yet
-->




