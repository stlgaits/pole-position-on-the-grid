---
layout: image-right
image: /toto_wolff.jpg
backgroundSize: contain
---

# What if... you were Toto Wolff?

Let's create a Formula 1 admin panel using :

* the [OpenF1](https://openf1.org/) API
* the Sylius Stack
    * boosted `make:grid` command
    * new Sylius Grid Attributes
        * #[AsGrid]
        * #[AsFilter]

---

Create a Driver resource

```php
namespace App\Resource;

use Sylius\Resource\Metadata\AsResource;
use Sylius\Resource\Model\ResourceInterface;

#[AsResource]
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

## Create a Driver grid

<v-clicks>

💡 <em>Now you can also create a grid based on a non-Doctrine entity, including... Sylius Resources !</em>

```shell
symfony console make:grid 'App\Resource\DriverResource'
```

<img src="/new_make_grid.png" class="w-150"/>

</v-clicks>

---

```php
namespace App\Grid;

use Sylius\Bundle\GridBundle\Builder\Field\StringField;
use Sylius\Bundle\GridBundle\Builder\GridBuilderInterface;
use Sylius\Bundle\GridBundle\Grid\AbstractGrid;
use Sylius\Component\Grid\Attribute\AsGrid;

#[AsGrid]
final class DriverGrid extends AbstractGrid
{
    public function buildGrid(GridBuilderInterface $gridBuilder): void
    {
        $gridBuilder
            ->addField(TwigField::create('image', 'driver/grid/field/image.html.twig'))
            ->addField(StringField::create('number'))
            ->addField(StringField::create('firstName'))
            ->addField(StringField::create('lastName'))
            ->addField(StringField::create('countryCode'))
            ->addField(StringField::create('teamName'))
        ;
    }
}
```

---
layout: center
---

𝄜 The brand new #[AsGrid] attribute

---
layout: two-cols
---

Before

```php
use App\Entity\Dummy;
use Sylius\Bundle\GridBundle\Grid\AbstractGrid;
use Sylius\Bundle\GridBundle\Grid\ResourceAwareGridInterface;

final class DummyGrid extends AbstractGrid 
implements ResourceAwareGridInterface
{
    public function buildGrid(
        GridBuilderInterface $gridBuilder,
    ): void {
        // ...
    }
    
    public function getResourceClass(): string
    {
        return Dummy::class;
    } 
}
```

::right::

After

```php
use App\Entity\Dummy;
use Sylius\Bundle\GridBundle\Grid\AbstractGrid;

#[AsGrid(resourceClass: Dummy::class)]
final class DummyGrid extends AbstractGrid
{
    public function __invoke(
        GridBuilderInterface $gridBuilder,
    ): void 
        // ...
    }
}
```

---
layout: center
---

```php{all|2,4,7}
use App\Entity\Dummy;
use Sylius\Bundle\GridBundle\Grid\AbstractGrid;

#[AsGrid(resourceClass: Dummy::class)]
final class DummyGrid extends AbstractGrid
{
    public function __invoke(
        GridBuilderInterface $gridBuilder,
    ): void 
        // ...
    }
}
```


---
layout: center
class: bg-black text-white
---

Starting slowly

<img src="/formation_lap.gif">

---

Create a driver 🏎️ Grid Provider

```php
namespace App\Grid;

use Pagerfanta\Adapter\FixedAdapter;
use Pagerfanta\Pagerfanta;
use Pagerfanta\PagerfantaInterface;
use Sylius\Component\Grid\Data\DataProviderInterface;
use Sylius\Component\Grid\Definition\Grid;
use Sylius\Component\Grid\Parameters;

final readonly class DriverGridProvider implements DataProviderInterface
{
    public function getData(Grid $grid, Parameters $parameters): PagerFantaInterface
    {
        // start with an empty paginator
        return new Pagerfanta(new FixedAdapter(0, []));
    }
}
```

---

```php {all|11}
// ...
use Sylius\Bundle\GridBundle\Grid\AbstractGrid;
use Sylius\Component\Grid\Attribute\AsGrid;

#[AsGrid]
final class DriverGrid extends AbstractGrid
{
    public function buildGrid(GridBuilderInterface $gridBuilder): void
    {
        $gridBuilder
            ->setProvider(DriverGridProvider::class)
            // ...
        ;
    }
}
```

---
layout: image
image: '/empty_data.png'
backgroundSize: contain
---


---
layout: center
class: bg-black text-white
---

Now, let's use real data from the API!

<img src="/starting_grid.gif">


---
transition: fade
---

```php
namespace App\Grid\Provider;

use Pagerfanta\Adapter\ArrayAdapter;
use Pagerfanta\Pagerfanta;
use Pagerfanta\PagerfantaInterface;
use Sylius\Component\Grid\Data\DataProviderInterface;
use Sylius\Component\Grid\Definition\Grid;
use Sylius\Component\Grid\Parameters;
use Symfony\Contracts\HttpClient\HttpClientInterface;

final readonly class DriverGridProvider implements DataProviderInterface
{
    public function __construct(
        private HttpClientInterface $openF1Client,
    ) {}

    public function getData(Grid $grid, Parameters $parameters): PagerFantaInterface
    {
        return new Pagerfanta(new ArrayAdapter(iterator_to_array($this->getDrivers())));
    }

    private function getDrivers(): iterable
    {
        // ...
    }
}
```

---

```php {all|16|18-27}
namespace App\Grid\Provider;

use Sylius\Component\Grid\Data\DataProviderInterface;
use Symfony\Contracts\HttpClient\HttpClientInterface;

final readonly class DriverGridProvider implements DataProviderInterface
{
    public function __construct(
        private HttpClientInterface $openF1Client
    ) {}

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
layout: image
image: '/grid_with_api.png'
backgroundSize: contain
---

---
layout: center
---

## 🔎 Filter for specific drivers

using the brand new #[AsFilter] attribute

---
transition: fade
---

Before

```php {all|5,2|5,2,12,17}
use Sylius\Component\Grid\Data\DataSourceInterface;
use Sylius\Component\Grid\Filtering\ConfigurableFilterInterface;
use Symfony\Component\Form\Extension\Core\Type\CountryType;

final class CountryFilter implements ConfigurableFilterInterface
{
    public function apply(DataSourceInterface $dataSource, string $name, $data, array $options): void
    {
        // TODO: Implement apply() method.
    }
    
    public static function getType(): string
    {
        return self::class;
    }
    
    public static function getFormType(): string
    {
        return CountryType::class;
    }
}
```

---

After

```php {1,6,7,3}
use Sylius\Component\Grid\Attribute\AsFilter;
use Sylius\Component\Grid\Data\DataSourceInterface;
use Sylius\Component\Grid\Filtering\FilterInterface;
use Symfony\Component\Form\Extension\Core\Type\CountryType;

#[AsFilter(formType: CountryType::class)]
final class CountryFilter implements FilterInterface
{
    public function apply(DataSourceInterface $dataSource, string $name, $data, array $options): void
    {
        // TODO: Implement apply() method.
    }
}
```

---
layout: two-cols
---

Before

```yaml
## config/packages/sylius_grid.yaml
sylius_grid:
  filter:
    dummy: 'path/to/dummy/filter/template.html.twig'
```

::right::

After

```php
use Sylius\Component\Grid\Attribute\AsFilter;
use Sylius\Component\Grid\Filtering\FilterInterface;

#[AsFilter(
    template: 'path/to/dummy/filter/template.html.twig',
)]
final class DummyFilter implements FilterInterface
{
    // ..
}
```


---
transition: fade
---


## #[AsFilter]
```php {all|8,3|9,6|10|12-18,5|all}
namespace App\Grid\Filter;

use Sylius\Component\Grid\Attribute\AsFilter;
use Sylius\Component\Grid\Data\DataSourceInterface;
use Sylius\Component\Grid\Filtering\FilterInterface;
use Symfony\Component\Form\Extension\Core\Type\CountryType;

#[AsFilter(
    formType: CountryType::class, // Symfony Form Type to use
    template: '@SyliusBootstrapAdminUi/shared/grid/filter/select.html.twig', // The Twig template
)]
final class CountryFilter implements FilterInterface
{
    public function apply(DataSourceInterface $dataSource, string $name, $data, array $options): void
    {
        // We handle the filtering part in the DriverGridProvider
    }
}
```

---

## Insert the filter into our Grid

```php {all|7,14|8-12|all}
#[AsGrid]
final class DriverGrid extends AbstractGrid
{
    public function buildGrid(GridBuilderInterface $gridBuilder): void
    {
        $gridBuilder
            ->addFilter(
                Filter::create('country', CountryFilter::class)
                    ->setFormOptions([
                        'alpha3' => true,
                        'autocomplete' => true,
                    ])
                    ->setLabel('app.ui.country')
            )
            // ...
        ;
    }
}

```

---
transition: fade
---

## Actual filtering logic inside the provider

```php {all|9-11|14|26|all}
final readonly class DriverApiGridProvider implements DataProviderInterface
{
    // ...

    private function getDrivers(array $criteria): iterable
    {
        $query = ['session_key' => 9158];
    
        if (!empty($criteria['country'] ?? null)) {
            $query['country_code'] = $criteria['country'];
        }
    
        $responseData = $this->openF1Client
            ->request(method: 'GET', url: '/v1/drivers', options: ['query' => $query])
            ->toArray()
        ;

        foreach ($responseData as $row) {
            yield new DriverResource(
                number: $row['driver_number'],
                // ...
            );
        }
    }
}
```

---
transition: fade
layout: image
image: '/country_filter.png'
backgroundSize: contain
---

---
layout: image
image: '/country_filter_results.png'
backgroundSize: contain
---

---
layout: center
---