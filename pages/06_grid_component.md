---
layout: center
---

## Push, Push, Push!

<img src="/push_lewis.gif" class="w-120">

---
layout: center
---

# 🧟 New Grid Live Component

it's alive...

---
layout: image
image: /pit_stop.gif
---

---
layout: two-cols
---

Overview of the new DataTableComponent

```php {all|5,8,11,14,17}
#[AsLiveComponent(name: 'sylius_grid_data_table')]
final class DataTableComponent
{
    #[LiveProp(writable: true)]
    public string|null $grid = null;

    #[LiveProp(writable: true)] 
    public int $page = 1;
    
    #[LiveProp(writable: true)]
    public array|null $criteria = null;

    #[LiveProp(writable: true)]
    public array|null $sorting = null;

    #[LiveProp(writable: true)]
    public int|null $limit = null;
}
```

::right::

Transform your grid into a Live Component

```yaml {none|all|7-11}
sylius_twig_hooks:
    hooks:
        'sylius_admin.book.index.content.grid':
            data_table:
                component: 'sylius_grid_data_table'
                props:
                    grid: '@=_context.grid'
                    page: '@=_context.page'
                    criteria: '@=_context.criteria'
                    sorting: '@=_context.sorting'
                    limit: '@=_context.limit'                    
```

Use it in any template

```twig {none|all|4-6}
<!-- templates/book/show/body.html.twig -->
{{ component('sylius_grid_data_table', {
    grid: 'app_admin_book',
    criteria: {
        author: author.id,
    },
}) }}
```
