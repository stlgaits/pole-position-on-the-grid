---
layout: center
---

# Going further with new Grid attributes

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

🔎 The brand new #[AsFilter] attribute

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

```php {1,6}
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
    dummy: 'path/to/dummy/filter/template'
```

::right::

After

```php
use Sylius\Component\Grid\Attribute\AsFilter;
use Sylius\Component\Grid\Filtering\FilterInterface;

#[AsFilter(
    template: 'path/to/dummy/filter/template',
)]
final class DummyFilter implements FilterInterface
{
    // ..
}
```

---

TODO
Talk about specifying the custom data provider on the AsGrid attribute.
