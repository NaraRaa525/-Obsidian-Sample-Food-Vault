<%*
const fileName = await tp.system.prompt("New Food")

const category = await tp.system.suggester((item) => item, ["Ingredient", "Recipe"])

let filePath = `98-All the Food/${category}/${fileName}`
//console.log(filePath)

if(filePath.includes("null"||"Null")) {
	console.log("error!")
} else {
	await tp.file.create_new(
	tp.file.find_tfile(`TP - ${category}`), fileName, true)
	await tp.file.move(`${filePath}`)
}

-%>
