# wurst-item-recipes
 
This library allows you to make simple item recipes. If all required items of a recipe are picked up, they will be replaced by the result item.

Additionally you may wrap your `ItemDefinition`s with `RecipeItem`, which creates a tome dummy allowing for pick up with full inventory.
This means units will never receive a "full inventory" warning and always atempt a pick up.
This way they may pick up recipe items while having a full inventory.
You don't have to wrap your `ItemDefinition`s if you don't want this behavior.

Example:

```wurst
constant ITEM1_DEF = compiletime(new RecipeItem()
    ..setName("Test Item 1")
    ..setGoldCost(100)
    ..setInterfaceIcon(Icons.bTNRingGreen)
    ..build())
    ..register()

constant ITEM2_DEF = compiletime(new RecipeItem()
    ..setName("Test Item 2")
    ..setGoldCost(100)
    ..setInterfaceIcon(Icons.bTNRingGreen)
    ..setTintingColor1Red(100)
    ..build())
    ..register()

constant ITEM3_DEF = compiletime(new RecipeItem()
    ..setName("Test Item 3")
    ..setGoldCost(100)
    ..setInterfaceIcon(Icons.bTNRingGreen)
    ..setTintingColor1Red(100)
    ..build())
    ..register()

constant RECIPE_DEF = new ItemRecipe("Ring Recipe")
    ..setRequiredItems(ITEM1_DEF.getNewId(), ITEM2_DEF.getNewId())
    ..setResultItem(ITEM3_DEF.getNewId())
```