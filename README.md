# wurst-item-recipes

Requires wurst-bonus-handler for adding bonuses to items.

`grill install https://github.com/Frotty/wurst-bonus-handler`
`grill install https://github.com/Frotty/wurst-item-recipes:main`
 
This library allows you to make simple item recipes. If all required items of a recipe are picked up, they will be replaced by the result item.

Example Recipe:

```wurst
constant RECIPE_DEF = new ItemRecipe()
    ..setRequiredItems(RECIPE_ITEM_1_ID, RECIPE_ITEM_2_ID)
    ..setResultItem(TARGET_ITEM_ID.getNewId())
```

## The `RecipeItem` wrapper

Additionally you may replace your `ItemDefinition`s with `RecipeItem`, which creates a tome dummy allowing for pick up with full inventory.
This means units will never receive a "full inventory" warning and always atempt a pick up.
This way they are able to pick up items (e.g. recipes) while having a full inventory.
You don't have to replace your `ItemDefinition`s if you don't want this behavior, recipes will still work normally.

To replace your ItemDefinitions, replace your object allocations from `ItemDefinition` to `RecipeItem`. You may remove any static or generated object id you've used before (example below). Then, if not already the case, move the RecipeItem allocation into a compiletime expression. This evaluates the result at compiletime, giving us access to the item's properties at runtime. Call `.register()` on that result, to make the item known at runtime.

Example:

```wurst
// BEFORE
constant MY_ITEM_ID = ITEM_ID_GEN.next()

@compiletime function foo()
    new ItemDefinition(MY_ITEM_ID, 'wolg')
    ..setName("My Item")
    ...
```

```wurst
// AFTER
constant MY_ITEM_DEF = compiletime(new RecipeItem(MY_ITEM_ID, 'wolg')
    ..setName("My Item"))
    ..register()
```

Since the id handling is now done internally, you may remove input ids.

```wurst
// AFTER (SHORT VERSION)
constant MY_ITEM_DEF = compiletime(new RecipeItem()
    ..setName("My Item"))
    ..register()
```

## Full Example

```wurst
package RecipesTest
import ClosureTimers
import RecipeItem
import Icons
import ItemRecipe

constant ITEM1_DEF = compiletime(new RecipeItem()
    ..setName("Test Item 1")
    ..setGoldCost(100)
    ..setInterfaceIcon(Icons.bTNRingGreen))
    ..register()

constant ITEM2_DEF = compiletime(new RecipeItem()
    ..setName("Test Item 2")
    ..setGoldCost(100)
    ..setInterfaceIcon(Icons.bTNRingGreen)
    ..setTintingColor1Red(100))
    ..register()

constant ITEM3_DEF = compiletime(new RecipeItem()
    ..setName("Test Item 3")
    ..setGoldCost(100)
    ..setInterfaceIcon(Icons.bTNRingGreen)
    ..setTintingColor1Red(100))
    ..register()

constant RECIPE_DEF = new ItemRecipe()
    ..setRequiredItems(ITEM1_DEF.getNewId(), ITEM2_DEF.getNewId())
    ..setResultItem(ITEM3_DEF.getNewId())


init
    nullTimer() -> 
        ITEM1_DEF.create(ZERO2)
        ITEM2_DEF.create(ZERO2)
```

