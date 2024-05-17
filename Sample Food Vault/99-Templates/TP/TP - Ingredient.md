---
<%*
// Select food tag
let foodType = await tp.system.suggester((item) => item, [
"Grain", "Meat", "Fruit", "Veggie", "EggnDairy", "Seasoning", "Snack", "Beverage"
])

// NRV prompt box
let NRV = await tp.system.prompt("NRV: Weight(g), Calories(kJ), Protein, Fat, CH, Sodium")

// Function for avoiding the appearance of long decimals
function shortenFloat(floatNumber) {
    const roundedFloat = Math.round(floatNumber * 100) / 100
    return roundedFloat
}
// Get nutrition value per gram/mL
let calorie = shortenFloat(NRV.split(" ")[1]/NRV.split(" ")[0])
let protein = shortenFloat(NRV.split(" ")[2]/NRV.split(" ")[0])
let fat = shortenFloat(NRV.split(" ")[3]/NRV.split(" ")[0])
let CH = shortenFloat(NRV.split(" ")[4]/NRV.split(" ")[0])
let sodium = shortenFloat(NRV.split(" ")[5]/NRV.split(" ")[0])
-%>
tags: Ingredient/<%foodType%>
---
For every 1 gram/mL of this [[All the Food#Ingredients|Ingredient]]:

| Nutrition            | Amount                       |
| -------------------- | ---------------------------- |
| Calorie              | (calorie:: <%calorie%>) kJ     |
| Protein              | (protein:: <%protein%>) g      |
| Fat                  | (fat:: <%fat%>) g             |
| Carbohydrate         | (CH:: <%CH%>) g               |
| Sodium               | (sodium:: <%sodium%>) mg       |

Preset for Reference: (presetWeight:: <%await tp.system.prompt("Preset Weight")%>)g per (unit:: <%await tp.system.prompt("Preset Unit")%>)
