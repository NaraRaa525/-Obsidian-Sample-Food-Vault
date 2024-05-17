<%*
async function getSelectedFile(tag) {
    const tagFiles = app.vault.getMarkdownFiles().filter(file => {
        const tags = tp.obsidian.getAllTags(app.metadataCache.getFileCache(file))
        return tags.some(t => t === tag || t.startsWith(tag + "/"))
    })
    return (await tp.system.suggester((file) => file.basename, tagFiles)).basename
}

async function insertFication(file, position, appendContent) {
    let content = await app.vault.read(file);
    let headerIndex = content.indexOf(position);
    let insertionIndex = content.indexOf("\n", headerIndex);
    const modifiedContent = content.slice(0, insertionIndex) + "\n" + appendContent + content.slice(insertionIndex);
    await app.vault.modify(file, modifiedContent);
}

let ingOrRec = await tp.system.suggester((item) => item, ["Ingredient", "Recipe"])

if(ingOrRec === "Ingredient") {
	const selectedFile = await getSelectedFile("#Ingredient")
	 // Setting Attributes for Display and Future Calculation Purpose
	const fetchProperty = DataviewAPI.page(tp.file.find_tfile(selectedFile).path)
	const boolean = await tp.system.suggester((item) => item, ["Use Preset Weight", "Input Weight Manually"])
	const preset = boolean === "Use Preset Weight" ? fetchProperty.presetWeight : 1
	
		//if we use preset weight, for example, 125g blueberries per box, then the amount beneath refers to "how many boxes of blueberries"
		// if we do a manual input, for example, 10g mayo, then the amount = 10
		const amount = await tp.system.prompt("Amount")
		const finalString = `- [[${selectedFile}]] [amount:: ${amount}] [preset:: ${preset}]`
		insertFication(tp.config.active_file, "## List", finalString)
}
if(ingOrRec === "Recipe") {
		const selectedFile = await getSelectedFile("#Recipe")
		const amount = await tp.system.prompt("Amount")
		const finalString = `- [[${selectedFile}]] [amount:: ${amount}]`
		insertFication(tp.config.active_file, "## List", finalString)
}
-%>