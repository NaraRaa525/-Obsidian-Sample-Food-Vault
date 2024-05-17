<%*  
// Get all files with tag-Ingredient, and choose the one that we need to use
async function getSelectedFile(tag) {
    const tagFiles = app.vault.getMarkdownFiles().filter(file => {
        const tags = tp.obsidian.getAllTags(app.metadataCache.getFileCache(file))
        return tags.some(t => t === tag || t.startsWith(tag + "/"))
    })
    return (await tp.system.suggester((file) => file.basename, tagFiles)).basename
}

const selectedFile = await getSelectedFile("#Ingredient")

// Setting Attributes for Display and Future Calculation Purpose
const fetchProperty = DataviewAPI.page(tp.file.find_tfile(selectedFile).path)
const boolean = await tp.system.suggester((item) => item, ["Use Preset Weight", "Input Weight Manually"])
const preset = boolean === "Use Preset Weight" ? fetchProperty.presetWeight : 1

	//if we use preset weight, for example, 125g blueberries per box, then the amount beneath refers to "how many boxes of blueberries"
	// if we do a manual input, for example, 10g mayo, then the amount = 10
	const amount = await tp.system.prompt("Amount")

// Update Property
setTimeout(() => {  
app.fileManager.processFrontMatter(tp.config.target_file, frontmatter => {
	
	function formatIngredient(Ingredient, Amount, Preset) {
	let obj = {}
	obj["name"] = `[[${Ingredient}]]`
	obj["amount"] = parseFloat(Amount)
	obj["preset"] = parseFloat(Preset)
	return obj
	}
	
	let newIng = formatIngredient(selectedFile, amount, preset)

	// Check if frontmatter['ingredients'] is defined and is an array. 
	// If not, then start a new array; else concat() new ingredient and its attributes to property
	if (!frontmatter['ingredients'] || !Array.isArray(frontmatter['ingredients'])) {
	    frontmatter['ingredients'] = []
	}
		frontmatter['ingredients'] = frontmatter['ingredients'].concat(newIng)
	})  
}, 200) 
-%>
