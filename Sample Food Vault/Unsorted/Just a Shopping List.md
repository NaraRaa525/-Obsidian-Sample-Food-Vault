Related Template:: [[Function - Input Assistance for Shopping List]] - `Alt + A`

Eventually I find a shopping list has no practical use for me. I decide what dishes to make based on what ingredients I have. Let's say, the price of tomatoes increased to 1.25x this week. Even if tomatoes are on the shopping list, i am not willing to buy it, and I might hold till next week to see whether its price would decrease or not.

Since making the code below has almost depleted my sanity, I will leave it here just for someone who find it useful.

## List


###
```dataviewjs
const resultsIng = await dv.query(`
table without id
	foods as Ingredient,
	amount, preset, unitWeight
where file = this.file
flatten file.lists as food
flatten food.amount as amount
flatten food.preset as preset
flatten food.outlinks as foods
flatten foods.presetWeight as unitWeight
where contains(foods.tags, "Ingredient")
`)
const valueIng = resultsIng.value.values

const ingList = valueIng.map(array => array[0])
const ingAmountInUnit = valueIng.map(array => {
	const foodAmount = array[1]
	const foodPreset = array[2]
	const unitWeight = array[3]
	return foodAmount * foodPreset / unitWeight
})

// Ing End
// Recipe Start
const resultsRecipe = await dv.query(`
table without id
	foodName as Food,
	map(foodAmounts, (f) => f * amount),
	foodPresets,
	unitWeight
where file = this.file
flatten file.lists as food
flatten food.amount as amount
flatten food.outlinks.ingredients.name as foodName
flatten food.outlinks.ingredients.amount as foodAmounts
flatten food.outlinks.ingredients.preset as foodPresets
flatten food.outlinks.ingredients.name.presetWeight as unitWeight
where contains(food.outlinks.tags, "Recipe")`)

const valueRecipe = resultsRecipe.value.values

// trim filePath to its relative form instead of the absolute one
valueRecipe.forEach((arr) => {
  arr[0].forEach((link) => {
    const fileName = link.path.split('/').pop().replace('.md', '')
    link.display = fileName
    link.path = fileName
  })
})

// function to do calcuation
function som(thing) {
const result = thing[0].map((_, index) => {
  return thing.slice(1).reduce((acc, subArray) => {
    return Math.round(acc * subArray[index] / thing[2][index] * 100) / 100;
  }, thing[0][index]);
});
return result
}

let recIngNameList = valueRecipe.map(f => f[0]).flat()
let recIngAmountList = valueRecipe.map(f => f.slice(1)).map(subArray => som(subArray)).flat()


// Recipe End
// Merge-fication Start

let RecWIP = [recIngNameList, recIngAmountList]
let IngWIP = [ingList, ingAmountInUnit]

// The following is a two-week-ago work and i definitely dont know how I get here, as long as it functions i dont really care much i mean i dont even want to consider the grammar of this line now ; my sanitys touching bottom at any moment
let originalData = [RecWIP, IngWIP]


const processData = (data) => {
  const result = {}
  data.forEach(([foods, values]) => {
    foods.forEach((food, index) => {
      if (!result[food]) {
        result[food] = []
      }
      result[food].push(values[index])
    })
  })
  return result
}

const mergeValues = (processedData) => {
  const mergedResult = {}
  Object.keys(processedData).forEach((food) => {
    const sum = processedData[food].reduce((acc, val) => acc + val, 0)
    mergedResult[food] = sum
  })

  return mergedResult
}

const processedData = processData(originalData)
const mergedResult = mergeValues(processedData)

const finalResult = Object.keys(mergedResult).reduce((acc, food) => {
  acc[0].push(food)
  acc[1].push(mergedResult[food])
  return acc
}, [[], []])

//console.log(originalData)
dv.table(['Food','Amount in Preset Unit'], [finalResult])



``` 


