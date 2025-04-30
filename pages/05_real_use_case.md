---
layout: center
---

# Real use case

Let's create an admin panel to manage conference talks

---

Create a Pokémon resource

```php
namespace App\Resource;

use Sylius\Resource\Metadata\AsResource;
use Sylius\Resource\Model\ResourceInterface;

#[AsResource]
final readonly class PokemonResource implements ResourceInterface
{
    public function __construct(
        public string $id,
        public int $height,
        public int $weight,
    ) {
    }

    public function getId(): string
    {
        return $this->id;
    }
}

```

---

Create a Pokémon grid

```shell
symfony console make:grid 'App\Resource\PokemonResource'
```

---

```php
namespace App\Grid;

use Sylius\Bundle\GridBundle\Builder\Field\StringField;
use Sylius\Bundle\GridBundle\Builder\GridBuilderInterface;
use Sylius\Bundle\GridBundle\Grid\AbstractGrid;
use Sylius\Component\Grid\Attribute\AsGrid;

#[AsGrid]
final class PokemonGrid extends AbstractGrid
{
    public function buildGrid(GridBuilderInterface $gridBuilder): void
    {
        $gridBuilder
            ->addField(StringField::create('id')
            ->addField(StringField::create('height'))
            ->addField(StringField::create('weight'))
        ;
    }
}
```

---

Create a Pokémon grid provider

```php {all|10|6,10|12|12,5|14-15}
namespace App\Grid;

use Pagerfanta\Adapter\FixedAdapter;
use Pagerfanta\Pagerfanta;
use Pagerfanta\PagerfantaInterface;
use Sylius\Component\Grid\Data\DataProviderInterface;
use Sylius\Component\Grid\Definition\Grid;
use Sylius\Component\Grid\Parameters;

final readonly class PokemonGridProvider implements DataProviderInterface
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
final class PokemonGrid extends AbstractGrid
{
    public function buildGrid(GridBuilderInterface $gridBuilder): void
    {
        $gridBuilder
            ->setProvider(PokemonGridProvider::class)
            // ...
        ;
    }
}
```

---

<img class="w-150" src="/empty_data.png"/>

---

```php {all|7-8|11-16}
// ...

final readonly class PokemonGridProvider implements DataProviderInterface
{
    public function getData(Grid $grid, Parameters $parameters): PagerFantaInterface
    {
        // start with a fixed data paginator
        return new Pagerfanta(new FixedAdapter(3, $this->getPokemon()));
    }

    private function getPokemon(): iterable
    {
        yield new PokemonResource(id: 'bulbasaur', height: 7, weight: 69);
        yield new PokemonResource(id: 'ivysaur', height: 10, weight: 130);
        yield new PokemonResource(id: 'venusaur', height: 20, weight: 1000);
    }
}
```

---

<img class="w-150" src="/fixed_data.png"/>

---

TODO
* Retrieve data from PokéAPI directly on the grid provider
* Create a dedicated repository
* Create a grid filter for Pokémon types
* Create a types grid
* Add a link to Pokémon list from a specific type
