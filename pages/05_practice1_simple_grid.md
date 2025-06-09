---
layout: center
hideInToc: true
---

# Practice 1: let's generate a simple grid view

![Simple Drivers Grid](/basic_driver_grid.png)

---
hideInToc: true
---

# What do we need to generate an index grid?

* **routing & operations** (Sylius Resource attributes)
* a data **model** (can be a Sylius Resource)
* a **data provider** (Doctrine, API repo, etc)
* a **Sylius Grid** definition (structure: fields, filters, sorting, pagination and actions)

---

## 1- Create a Resource

```php {all|3-5|all}
#[AsResource(
    templatesDir: '@SyliusAdminUi/crud',
    operations: [
        new Index(grid: DriverGrid::class),
    ],
)]
final readonly class DriverResource implements ResourceInterface
{
    public function __construct(
        public int $number,
        public string $firstName,
        public string $lastName,
        public string $countryCode,
        public string $teamName,
        public string|null $image = null,
    ) {
    }

    public function getId(): int
    {
        return $this->number;
    }
}
```

---

## 2- Create a custom data Provider

```php
use Sylius\Component\Grid\Data\DataProviderInterface;
use Symfony\Contracts\HttpClient\HttpClientInterface;

final readonly class DriverGridProvider implements DataProviderInterface
{
    // ...
    
    private function getDrivers(): iterable
    {
        $responseData = $this->openF1Client->request('GET', '/v1/drivers?session_key=9158')->toArray();

        foreach ($responseData as $row) {
            yield new DriverResource(
                number: $row['driver_number'],
                firstName: $row['first_name'],
                lastName: $row['last_name'],
                countryCode: $row['country_code'],
                teamName: $row['team_name'],
                image: $row['headshot_url'],
            );
        }
    }
}
```

---
layout: center
---

𝄜 Improved `make:grid` console command

---

## 3- Create a Grid

<v-clicks>

💡 <em>Now you can also create a grid based on a non-Doctrine entity, including... Sylius Resources !</em>

```shell
symfony console make:grid 'App\Resource\DriverResource'
```

<img src="/new_make_grid.png" class="w-150"/>

</v-clicks>

---


## 3- Create a Grid

```php
final class DriverResourceGrid extends AbstractGrid implements ResourceAwareGridInterface
{
    public function __construct() {  // TODO inject services if required  }
    
    public static function getName(): string
    {
        return 'app_driver_resource';
    }
    
    public function getResourceClass(): string
    {
        return DriverResource::class;
    }
    
    public function buildGrid(GridBuilderInterface $gridBuilder): void
    {
        $gridBuilder
            ->addField(StringField::create('firstName')->setLabel('FirstName')->setSortable(true))
           // ... all fields are added except "id"
            ->addActionGroup(MainActionGroup::create(CreateAction::create()))
            ->addActionGroup(BulkActionGroup::create(DeleteAction::create()))
            ->addActionGroup(ItemActionGroup::create(
                    // ShowAction::create(),
                    UpdateAction::create(),
                    DeleteAction::create()))
        ;
    }
}

```

---

## 4- Add provider to grid

```php {all|6|all}
final class DriverGrid extends AbstractGrid implements ResourceAwareGridInterface
{
    public function buildGrid(GridBuilderInterface $gridBuilder): void
    {
        $gridBuilder
            ->setProvider(DriverGridProvider::class)
            ->addField(
                StringField::create('firstName')
                ->setLabel('FirstName')
                ->setSortable(true)
                )
            // ...
        ;
    }
}
```


---
layout: image
image: '/grid_with_api.png'
backgroundSize: contain
---

