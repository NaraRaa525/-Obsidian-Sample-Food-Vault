---
weight: 0
calorie: 0
protein: 0
fat: 0
CH: 0
sodium: 0
---
## Daily Food Record

>[!example]- Calorie Calculation
>```dataviewjs
const results = await dv.query(`
table without id
food.outlinks as Food,
weight as "Weight(g)",
round(100 * weight * calorie)/100 as "Calorie(kJ)",
round(100 * weight * protein)/100 as "Protein(g)",
round(100 * weight * fat)/100 as "Fat(g)",
round(100 * weight * CH)/100 as "CH(g)",
round(100 * weight * sodium)/100 as "Sodium(mg)"
where file = this.file
flatten file.lists as food
flatten food.amount * food.preset as weight
flatten food.outlinks.calorie as calorie
flatten food.outlinks.protein as protein
flatten food.outlinks.fat as fat
flatten food.outlinks.CH as CH
flatten food.outlinks.sodium as sodium`)
const value = results.value.values
>
// Function to sum up the column and round the result
function getTotal(Results, Index) {
let num = Results.map(n => n[Index]).reduce((tmp, curr) => tmp + curr, 0)
return Math.round(num * 100)/100
}
>
// Sum and round the numbers
if (results.successful) {
  let totals = ["<strong>Total:</strong>"];
  for (let i = 1; i <= 6; i++) {
totals[i] = getTotal(value, i)
  }
value.push(totals)
// Generate md table
dv.table(results.value.headers, value)
>
// Update Properties
setTimeout(() => {
let tfile = app.vault.getAbstractFileByPath(dv.current().file.path)
app.fileManager.processFrontMatter(tfile, frontmatter => {
const properties = ['weight', 'calorie', 'protein', 'fat', 'CH', 'sodium']
frontmatter['weight'] = totals[1]
frontmatter['calorie'] = Math.round(totals[2]/4.11 *100)/100
for (let i = 2; i <= 5; i++) {
frontmatter[properties[i]] = totals[i + 1]
}
})
}, 200)
} else {
dv.paragraph("~~~~\n" + results.error + "\n~~~~~")}
>```

