<%*
const file = tp.file.find_tfile(tp.date.now("YYYY-MM-DD")
if(file){
const loggedItem = await tp.system.prompt("记录闪念")
const time = tp.date.now("HH:mm")
const content = (await app.vault.read(file)).split("\n")
const index = content.indexOf("# 闪念")
content.splice(index + 1,0, `- ${time} - ${loggedItem}`)
await app.vault.modify(file, content.join("\n"))
} else {
new Notification("日记还没有创建！")
}
-%>

