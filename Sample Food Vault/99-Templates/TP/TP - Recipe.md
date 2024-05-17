---
tags: Recipe
ingredients:
---

## Ingredient List
```dataview
table without id
	i.name as Ingredient,
	i.amount as Amount,
	i.amount * i.preset as "Weight(g)"
where file = this.file
flatten ingredients as i
```

## How to cook
- // Step 1 ... Reference Pictures If Needed etc ... Σ(っ °Д °;)っ...

## Nutrition Value Calculation
>[!example]- Nutrition Value Calculation
>```dataviewjs
const results = await dv.query(`
table without id 
	>i.name as Ingredient, 
	>weight as "Weight(g)",
	>round(100 * calorie) / 100 as "Calorie(kJ)",
	>round(100 * protein) / 100 as "Protein(g)",
	>round(100 * fat) / 100 as "Fat(g)",
	>round(100 * CH) / 100 as "CH(g)",
	>round(100 * sodium) / 100 as "Sodium(mg)"
where file = this.file
>
// Establish nutrition value
flatten ingredients as i 
flatten i.amount * i.preset as weight
flatten weight * i.name.calorie as calorie
flatten weight * i.name.protein as protein
flatten weight * i.name.fat as fat
flatten weight * i.name.CH as CH
flatten weight * i.name.sodium as sodium
`)
const value = results.value.values
>
// Function to sum up the column and round the result
function getTotal(Results, Index) {
let num = Results.map(n => n[Index]).reduce((tmp, curr) => tmp + curr, 0)
return Math.round(num * 100)/100
}
>
// Sum and round the numbers, and Generate md table
if (results.successful) {
  let totals = ["<strong>Total:</strong>"];
  for (let i = 1; i <= 6; i++) {
    >totals[i] = getTotal(value, i)
  }
value.push(totals)
dv.table(results.value.headers, value)
>
// Update Properties
setTimeout(() => {
let tfile = app.vault.getAbstractFileByPath(dv.current().file.path)
app.fileManager.processFrontMatter(tfile, frontmatter => {
	>const properties = ['presetWeight', 'calorie', 'protein', 'fat', 'CH', 'sodium']
	>frontmatter['presetWeight'] = totals[1]
	>for (let i = 1; i <= 5; i++) {
    >frontmatter[properties[i]] = Math.round(totals[i + 1]/ totals[1] * 100)/ 100
}
})
}, 200)
} else {
dv.paragraph("~~~~\n" + results.error + "\n~~~~~")}
>```