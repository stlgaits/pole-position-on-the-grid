---
layout: center
image: /grid_start.jpg
backgroundSize: contain
---

# Quick practice before it's your time to shine!
Q1: How can you add a Team filter for the F1 drivers' grid?

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

Q2: Find the odd one out.

<v-clicks>

* ```#[AsFilter()] ```
* ```#[AsGrid()]```
* <span v-mark="{ at: 5, color: 'red', type: 'circle' }">```#[AsProvider()]```</span>
* ```#[AsResource()]```

  
</v-clicks>

---

Q3: The OpenF1 API does not include a **/teams** endpoint, but you would like to create a grid listing all teams.
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

Q3: ... and the 'hard-coded' data provider below. 

```php
final readonly class TeamGridProvider implements DataProviderInterface
{
    public function getData(Grid $grid, Parameters $parameters): PagerFantaInterface
    {
        $teams = iterator_to_array($this->getTeamsPaginator());

        return new Pagerfanta(new ArrayAdapter($teams));
    }

    public function getTeamsPaginator(): iterable
    {
        $teams = [
            ['name' =>'Ferrari', 'color' => 'F91536'],
            ['name' =>'Mercedes', 'color' => '6CD3BF'],
            ['name' =>'Red Bull Racing', 'color' => '3671C6'],
            // ...
        ];

        foreach ($teams as $team) {
            yield new Team(
                id: $team['name'],
                name: $team['name'],
                color: $team['color'],
            );
        }
    }
}
```

---

Q3: What command could you run to generate the missing grid?

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


What's wrong with this code?


```php
    use Sylius\Component\Grid\Filtering\ConfigurableFilterInterface;

    #[AsFilter(formType: TeamFilterType::class,  template: FilterTemplate::SELECT->value,)]
    final class TeamFilter implements ConfigurableFilterInterface {
```

<!--
    @TODO replace this question with something more pertinent and interesting than just the wrong interface is implemented...
-->
