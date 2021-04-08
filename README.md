# wurst-item-recipes

Requires wurst-bonus-handler for adding bonuses to items.

`grill install https://github.com/Frotty/wurst-bonus-handler`

`grill install https://github.com/Frotty/wurst-item-recipes:main`
 
This library allows you to item recipes, meaning that if a list of **required items** is aquired, they will be replaced by a **result item**.

Example Recipe:

```wurst
constant RECIPE_DEF = new ItemRecipe()
    ..setRequiredItems(RECIPE_ITEM_1_ID, RECIPE_ITEM_2_ID)
    ..setResultItem(TARGET_ITEM_ID)
```

# Additional Features

For additional features you may replace your usages of `ItemDefinition` with `RecipeItem` as a drop in replacement.
Recipe items have the following benefits:
- Storing most important information about the item
- Stacking for items with stacks
- Picking up items with full inventory
- Applying bonuses easily
- Generating tooltips 


Using `RecipeItem will create a tome dummy allowing for pick up with full inventory.
This means units will never receive a "full inventory" warning and always atempt a pick up.
This way they are able to pick up items (e.g. recipes or stackable potions) while having a full inventory.

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
This will default to an item without effect. To use another item as base provide its id as only paremter.

```wurst
// AFTER, WITH NO ID GIVEN
constant MY_ITEM_DEF = compiletime(new RecipeItem()
    ..setName("My Item"))
    ..register()

// AFTER, WITH BASE ID GIVEN
constant MY_ITEM_DEF = compiletime(new RecipeItem(ItemIds.potionofHealing)
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

