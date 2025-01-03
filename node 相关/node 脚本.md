#### 递归修改文件名称
```
const path = require('path')
const fs = require('fs')

function join(name) {
  return path.join(__dirname, name)
}

const whiteFiles = [
  '资料',
  '资料-新',
  '资料及源码',
  '.DS_Store',
  'dragonslayer',
  'learn-typescript',
  'ts-with-react',
  'rename.js',
]

const write = (dir) => {
  const files = fs.readdirSync(join(dir))
  for (const file of files) {
    if (whiteFiles.includes(file)) continue
    if (fs.statSync(join(`${dir}/${file}`)).isDirectory()) {
      write(file)
    } else {
      const newName = file.replace('购课加qq：3125928112', '')
      fs.renameSync(join(`${dir}/${file}`), join(`${dir}/${newName}`))
    }
  }
}

write('./')

```