---
layout: center
hideInToc: true
---

# Practice 2: 𝄜 The brand new #[AsGrid] attribute

---
layout: center
---

## Resource class & provider arguments

```php
use App\Entity\Meeting;
use Sylius\Bundle\GridBundle\Grid\AbstractGrid;

#[AsGrid(
    provider: MeetingGridProvider::class,
    resourceClass: Meeting::class,   // any PHP model, including a Sylius resource
)]
final class MeetingGrid extends AbstractGrid
{
    public function __invoke(
        GridBuilderInterface $gridBuilder,
    ): void 
        // ...
    }
}
```

---

## build method and name arguments

```php
use App\Entity\Meeting;
use Sylius\Bundle\GridBundle\Grid\AbstractGrid;

#[AsGrid(
    buildMethod: 'customBuildMethod', 
    name: 'meeting', // optional - FQCN by default
    resourceClass: Meeting::class, // any PHP model, including a Sylius resource
)]
final class MeetingGrid extends AbstractGrid
{
    public function customBuildMethod(
        GridBuilderInterface $gridBuilder,
    ): void 
            $gridBuilder->setProvider(MeetingGridProvider::class)
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

* __invoke() instead of buildGrid() - but buildGrid still supported by default if no __invoke()

* buildMethod argument to declare custom build method

* provider argument instead of ->setProvider()

* name argument (FQCN is used by default)

</v-clicks>


---
layout: image
image: '/grid_start.jpg'
---

