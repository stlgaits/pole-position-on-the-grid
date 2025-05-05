---
layout: center
---

# Real use case

Let's create an admin panel with data provided by an external API

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

💡 <em>Creating a grid from non-Doctrine entity is sth new.</em>

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
class: bg-black text-white
---

Starting slowly

<img src="/formation_lap.gif">

---

Create a Driver grid provider

```php {all|10|6,10|12|12,5|14-15}
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

```php {all|7-8|11-17}
// ...

final readonly class DriverGridProvider implements DataProviderInterface
{
    public function getData(Grid $grid, Parameters $parameters): PagerFantaInterface
    {
        // start with a fixed data paginator
        return new Pagerfanta(new FixedAdapter(4, $this->getDrivers()));
    }

    private function getDrivers(): iterable
    {
        yield new DriverResource(number: 1, firstName: 'Max', lastName: 'Verstappen', countryCode: 'NED', teamName: 'Red Bull Racing', image: 'https://www.formula1.com/content/dam/fom-website/drivers/M/MAXVER01_Max_Verstappen/maxver01.png.transform/1col/image.png');
        yield new DriverResource(number: 2, firstName: 'Logan', lastName: 'Sargeant', countryCode: 'USA', teamName: 'Williams', image: 'https://www.formula1.com/content/dam/fom-website/drivers/L/LOGSAR01_Logan_Sargeant/logsar01.png.transform/1col/image.png');
        yield new DriverResource(number: 4, firstName: 'Lando', lastName: 'Norris', countryCode: 'GBR', teamName: 'McLaren', image: 'https://www.formula1.com/content/dam/fom-website/drivers/L/LANNOR01_Lando_Norris/lannor01.png.transform/1col/image.png');
        yield new DriverResource(number: 44, firstName: 'Lewis', lastName: 'Hamilton', countryCode: 'GBR', teamName: 'Mercedes', image: 'https://www.formula1.com/content/dam/fom-website/drivers/L/LEWHAM01_Lewis_Hamilton/lewham01.png.transform/1col/image.png');
    }
}
```


---
layout: image
image: '/fixed_data.png'
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

```php {all|14,9|17-20}
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
transition: fade
---

```php {19-22}
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
        private HttpClientInterface $openF1Client
    ) {}

    // ...

    private function getDrivers(): iterable
    {
        // ...
    }
}
```

---

```php {14|16|18-27}
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

## 🔎 Adding a filter

using the brand new #[AsFilter] attribute

---

```php {all|8,3|9,6|10|12-18,5}
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

```php {all|16|17,3|18-21}
namespace App\Grid;

use App\Grid\Filter\CountryFilter;
use Sylius\Bundle\GridBundle\Builder\Filter\Filter;
use Sylius\Bundle\GridBundle\Builder\GridBuilderInterface;
use Sylius\Bundle\GridBundle\Grid\AbstractGrid;
use Sylius\Component\Grid\Attribute\AsGrid;

#[AsGrid]
final class DriverGrid extends AbstractGrid
{
    public function buildGrid(GridBuilderInterface $gridBuilder): void
    {
        $gridBuilder
            // ...
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

```php {6,14|16}
namespace App\Grid\Provider;

use Sylius\Component\Grid\Data\DataProviderInterface;
use Symfony\Contracts\HttpClient\HttpClientInterface;

final readonly class DriverGridProvider implements DataProviderInterface
{
    public function __construct(
        private HttpClientInterface $openF1Client,
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

```php {12,17|20-23|26}
namespace App\Grid\Provider;
//...

final readonly class DriverApiGridProvider implements DataProviderInterface
{
    // ...

    public function getData(Grid $grid, Parameters $parameters): PagerFantaInterface
    {
        return new Pagerfanta(
            new ArrayAdapter(iterator_to_array(
                $this->getDrivers($parameters->get('criteria')),
            ))
        );
    }

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

Add a link to another grid with filtered data

---
layout: image
image: '/team_radio.png'
backgroundSize: contain
---

---

```php
namespace App\Grid;

use Sylius\Bundle\GridBundle\Builder\Filter\StringFilter;
use Sylius\Bundle\GridBundle\Builder\GridBuilderInterface;
use Sylius\Bundle\GridBundle\Grid\AbstractGrid;
use Sylius\Component\Grid\Attribute\AsGrid;

#[AsGrid]
final class TeamRadioGrid extends AbstractGrid
{
    public function buildGrid(GridBuilderInterface $gridBuilder): void
    {
        $gridBuilder
            // ...
            ->addFilter(
                StringFilter::create(name: 'driver_number', type: 'equal')
                    ->setLabel('app.ui.driver_number')
            )
            // ...
        ;
    }
}
```

---

```php {all|11,2|12,1|13-24}
use Sylius\Bundle\GridBundle\Builder\Action\Action;
use Sylius\Bundle\GridBundle\Builder\ActionGroup\ItemActionGroup;

#[AsGrid]
final class DriverGrid extends AbstractGrid
{
    public function buildGrid(GridBuilderInterface $gridBuilder): void
    {
        $gridBuilder
            ->addActionGroup(
                ItemActionGroup::create(
                    Action::create('team_radios', 'show')
                        ->setOptions([
                            'link' => [
                                'route' => 'app_admin_team_radio_index',
                                'parameters' => [
                                    'criteria' => [
                                        'driver_number' => [
                                            'value' => 'resource.number', // driverResource->number
                                        ],
                                    ],
                                ],
                            ],
                        ])
                        ->setLabel('app.ui.show_team_radios')
                        ->setIcon('tabler:radio'),
                )
            )
        ;
    }
}
```

---
transition: fade
layout: image
image: '/driver_with_linked_action.png'
backgroundSize: contain
---

---
layout: image
image: '/filtered_team_radio.png'
backgroundSize: contain
---
