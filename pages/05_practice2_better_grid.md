---
layout: center
---

𝄜 The brand new #[AsGrid] attribute

---
layout: two-cols
---

Before

```php
use App\Entity\Meeting;
use Sylius\Bundle\GridBundle\Grid\AbstractGrid;
use Sylius\Bundle\GridBundle\Grid\ResourceAwareGridInterface;

final class MeetingGrid extends AbstractGrid 
implements ResourceAwareGridInterface
{
    public function buildGrid(
        GridBuilderInterface $gridBuilder,
    ): void {
        // ...
    }
    
    public function getResourceClass(): string
    {
        return Meeting::class;
    } 
}
```

::right::

After

```php
use App\Entity\Meeting;
use Sylius\Bundle\GridBundle\Grid\AbstractGrid;

#[AsGrid(resourceClass: Meeting::class)]
final class MeetingGrid extends AbstractGrid
{
    public function __invoke(
        GridBuilderInterface $gridBuilder,
    ): void 
        // ...
    }
}
```
``
---
layout: center
---

```php{all|2,4,7}
use App\Entity\Meeting;
use Sylius\Bundle\GridBundle\Grid\AbstractGrid;

#[AsGrid(resourceClass: Meeting::class)]
final class MeetingDummyGrid extends AbstractGrid
{
    public function __invoke(
        GridBuilderInterface $gridBuilder,
    ): void 
        // ...
    }
}
```

<!--
* __invoke => - SOLID single responsibility / separation of concerns 
              - consistency with Symfony DX for services to be autowirable callables
              - no need to implement an interface anymore
* flexibility : you can still use buildGrid (it works behind the scenes without interface)
* custom build method => - Multiple grids def in one class
                         - Reusable logic (traits/base)
                         - Decorators/extensions
#[AsGrid('app_admin_user')]
#[AsGrid('app_admin_customer', buildMethod: 'buildCustomerGrid')]
final class UserGrids
{
    public function __invoke(GridBuilderInterface $grid): void
    {
        // User grid definition
    }

    public function buildCustomerGrid(GridBuilderInterface $grid): void
    {
        // Customer grid definition
    }
}
-->

---
layout: center
---

## [#AsGrid]

<v-clicks>
* __invoke() instead of buildGrid() - _but buildGrid still supporte_

</v-clicks>



---
layout: image
image: '/grid_start.jpg'
---

