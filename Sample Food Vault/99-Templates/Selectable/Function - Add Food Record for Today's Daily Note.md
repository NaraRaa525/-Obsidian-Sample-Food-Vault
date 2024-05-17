<%*
// Get the content of today's daily note 
let todaysNote = await tp.file.find_tfile(tp.date.now())
let content = await app.vault.read(todaysNote)

// Get the position that i want to insert food
let headerIndex = content.indexOf("## Daily Food Record")
let insertionIndex = content.indexOf("\n", headerIndex)

// Choose food file
let foodPath = "98-All the Food"
let file = (await tp.system.suggester((item) => item.basename, app.vault.getMarkdownFiles().filter(f => f.path.includes(foodPath))
)).basename

// Retrieve food weight and amount based on whether they use preset or not
const fileFrontmatter = DataviewAPI.page(tp.file.find_tfile(file).path)
let preset
let boolean = await tp.system.suggester((item) => item, ["Use Preset Weight", "Input Weight Manually"])
if (boolean == "Use Preset Weight") {preset = fileFrontmatter.presetWeight} else {preset = 1}
let amount = await tp.system.prompt("Amount")

const food = `- [[${file}]] [amount:: ${amount}] [preset:: ${preset}]`

// Do the thing
const modifiedContent = content.slice(0, insertionIndex) + "\n" + food + content.slice(insertionIndex);

await app.vault.modify(todaysNote, modifiedContent)
-%>