<%*
let content = await app.vault.read(tp.config.target_file)
let headerIndex = content.indexOf("## Food List")
let insertionIndex = content.indexOf("\n", headerIndex)

// Choose food file
const tag = "#Ingredient"; 
const filteredFiles = app.vault.getMarkdownFiles().filter(file => { const tags = tp.obsidian.getAllTags(app.metadataCache.getFileCache(file)); 
return tags.some(t => t === tag || t.startsWith(tag + "/")); });

const selectedFile = (await tp.system.suggester((file) => file.basename, filteredFiles)).basename;  

// Retrieve food weight and amount based on whether they use preset or not
const fileFrontmatter = DataviewAPI.page(tp.file.find_tfile(selectedFile).path)
let preset
let boolean = await tp.system.suggester((item) => item, ["Use Preset Weight", "Input Weight Manully"])
if (boolean == "Use Preset Weight") {preset = fileFrontmatter.presetWeight} else {preset = 1}
let amount = await tp.system.prompt("Amount")

const food = `- [[${selectedFile}]] [amount:: ${amount}] [preset:: ${preset}]`
const modifiedContent = content.slice(0, insertionIndex) + "\n" + food + content.slice(insertionIndex);

await app.vault.modify(tp.config.target_file, modifiedContent)
-%>
