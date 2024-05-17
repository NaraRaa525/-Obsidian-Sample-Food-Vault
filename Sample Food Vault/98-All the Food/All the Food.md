

## Recipes
```dataview
list
from #Recipe and -"99-Templates"
```

## Ingredients
```dataview
table 
	rows.file.link as Food
from #Ingredient and -"99-Templates"
sort file.name asc
group by file.etags as Category
```