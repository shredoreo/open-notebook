

# Settings Sync

[Shan Khan](https://marketplace.visualstudio.com/publishers/Shan)

## It Syncs

```
All extensions and complete User Folder that Contains
1. Settings File
2. Keybinding File
3. Launch File
4. Snippets Folder
5. VSCode Extensions & Extensions Configurations
6. Workspaces Folder
```

## Upload Your Settings

**Press Shift + Alt + U** (macOS: Shift + Option + U)

## Download your Settings

**Press Shift + Alt + D** (macOS: Shift + Option + D)

## Settings

Settings can be changed through the settings page, which can be accessed through **"> Sync : Advanced Options > Open Settings Page"**

There are two types of settings in Settings Sync. I will recommend you to read the configurations details [here](https://dev.to/shanalikhan/visual-studio-code-settings-sync-configurations-mn0).

### Gist Settings

Gist Settings are stored in `settings.json` file of Code. You can customize the settings in gist settings like:

```
1. Configure Gist Id (Environment)
2. Configure auto upload / download for GitHub Gist
3. Configure extension sync behaviour
4. Configure force download
4. Configure force upload
6. Configure quiet sync
    "sync.gist": "0c929b1a6c51015cdc9e0fe2e369ea4c",
    "sync.autoDownload": false,
    "sync.autoUpload": false,
    "sync.forceDownload": false,
    "sync.forceUpload": false,
    "sync.quietSync": false,
    "sync.removeExtensions": true,
    "sync.syncExtensions": true
```

### Global Settings

Global settings are present in `syncLocalSettings.json` inside `User` folder. These settings will be shared across multiple Gist Environments.

You can customize the sync:

```
1. Options by which files / folders and settings to exclude from upload.
2. Configure default Gist Environment name.
3. Replace the code settings after downloading.
4. Change the Gist description while creating new one in github.
5. Configure GitHub Enterprise Url
{
    "ignoreUploadFiles": [
        "state.*",
        "syncLocalSettings.json",
        ".DS_Store",
        "sync.lock",
        "projects.json",
        "projects_cache_vscode.json",
        "projects_cache_git.json",
        "projects_cache_svn.json",
        "gpm_projects.json",
        "gpm-recentItems.json"
    ],
    "ignoreUploadFolders": [
        "workspaceStorage"
    ],
    "ignoreExtensions": [],
    "gistDescription": "Visual Studio Code Settings Sync Gist",
    "version": 340,
    "token": "YOUR_GITHUB_TOKEN",
    "downloadPublicGist": false,
    "supportedFileExtensions": [ "json", "code-snippets" ],
    "openTokenLink": true,
    "disableUpdateMessage": false,
    "lastUpload": null,
    "lastDownload": null,
    "githubEnterpriseUrl": null,
    "askGistDescription": false,
    "customFiles": {},
    "hostName": null,
    "universalKeybindings": false,
    "autoUploadDelay": 20
}
```

I will recommend you to read the configurations details [here](https://dev.to/shanalikhan/visual-studio-code-settings-sync-configurations-mn0).



# JavaScript (ES6) code snippets



## Supported languages (file extensions)

- JavaScript (.js)
- TypeScript (.ts)
- JavaScript React (.jsx)
- TypeScript React (.tsx)
- Html (.html)
- Vue (.vue)

## Snippets

Below is a list of all available snippets and the triggers of each one. The **⇥** means the `TAB` key.

### Import and export

| Trigger | Content                                                      |
| :------ | :----------------------------------------------------------- |
| `imp→`  | imports entire module `import fs from 'fs';`                 |
| `imn→`  | imports entire module without module name `import 'animate.css'` |
| `imd→`  | imports only a portion of the module using destructing `import {rename} from 'fs';` |
| `ime→`  | imports everything as alias from the module `import * as localAlias from 'fs';` |
| `ima→`  | imports only a portion of the module as alias `import { rename as localRename } from 'fs';` |
| `rqr→`  | require package `require('');`                               |
| `req→`  | require package to const `const packageName = require('packageName');` |
| `mde→`  | default module.exports `module.exports = {};`                |
| `env→`  | exports name variable `export const nameVariable = localVariable;` |
| `enf→`  | exports name function `export const log = (parameter) => { console.log(parameter);};` |
| `edf→`  | exports default function `export default function fileName (parameter){ console.log(parameter);};` |
| `ecl→`  | exports default class `export default class Calculator { };` |
| `ece→`  | exports default class by extending a base one `export default class Calculator extends BaseClass { };` |

### Class helpers

| Trigger | Content                                                      |
| :------ | :----------------------------------------------------------- |
| `con→`  | adds default constructor in the class `constructor() {}`     |
| `met→`  | creates a method inside a class `add() {}`                   |
| `pge→`  | creates a getter property `get propertyName() {return value;}` |
| `pse→`  | creates a setter property `set propertyName(value) {}`       |

### Various methods

| Trigger  | Content                                                      |
| :------- | :----------------------------------------------------------- |
| `fre→`   | forEach loop in ES6 syntax `array.forEach(currentItem => {})` |
| `fof→`   | for ... of loop `for(const item of object) {}`               |
| `fin→`   | for ... in loop `for(const item in object) {}`               |
| `anfn→`  | creates an anonymous function `(params) => {}`               |
| `nfn→`   | creates a named function `const add = (params) => {}`        |
| `dob→`   | destructing object syntax `const {rename} = fs`              |
| `dar→`   | destructing array syntax `const [first, second] = [1,2]`     |
| `sti→`   | set interval helper method `setInterval(() => {});`          |
| `sto→`   | set timeout helper method `setTimeout(() => {});`            |
| `prom→`  | creates a new Promise `return new Promise((resolve, reject) => {});` |
| `thenc→` | adds then and catch declaration to a promise `.then((res) => {}).catch((err) => {});` |

### Console methods

| Trigger | Content                                                      |
| :------ | :----------------------------------------------------------- |
| `cas→`  | console alert method `console.assert(expression, object)`    |
| `ccl→`  | console clear `console.clear()`                              |
| `cco→`  | console count `console.count(label)`                         |
| `cdb→`  | console debug `console.debug(object)`                        |
| `cdi→`  | console dir `console.dir`                                    |
| `cer→`  | console error `console.error(object)`                        |
| `cgr→`  | console group `console.group(label)`                         |
| `cge→`  | console groupEnd `console.groupEnd()`                        |
| `clg→`  | console log `console.log(object)`                            |
| `clo→`  | console log object with name `console.log('object :>> ', object);` |
| `ctr→`  | console trace `console.trace(object)`                        |
| `cwa→`  | console warn `console.warn`                                  |
| `cin→`  | console info `console.info`                                  |
| `clt→`  | console table `console.table`                                |
| `cti→`  | console time `console.time`                                  |
| `cte→`  | console timeEnd `console.timeEnd`                            |