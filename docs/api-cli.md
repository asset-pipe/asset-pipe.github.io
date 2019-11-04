# CLI

## Command Summary

| command    | aliases | description                                                     |
| ---------- | ------- | --------------------------------------------------------------- |
| init       | i       | Create an assets.json file in the current directory             |
| version    | v       | Helper command for bumping your apps `asset.json` version field |
| publish    | p, pub  | Publish an app bundle                                           |
| dependency | d, dep  | Publish a dependency bundle                                     |
| map        | m       | Sets or deletes a "bare" import entry in an import-map file     |
| alias      | a       | Sets a major semver alias for a given dependency or map         |

## Commands Overview

### init

This command takes no input and creates a new `assets.json` file in the current directory with the following content:

```json
{
    "organisation": "[required]",
    "name": "[required]",
    "version": "1.0.0",
    "server": "http://assets-server.svc.prod.finn.no",
    "js": {
        "input": "[path to js entrypoint]",
        "options": {}
    },
    "css": {
        "input": "[path to css entrypoint]",
        "options": {}
    },
    "import-map": []
}
```

You will then need to change the various fields as appropriate. If you are running a local asset server, the default server url should be `http://localhost:4001`.

#### assets.json properties

| property     | description                                                         |
| ------------ | ------------------------------------------------------------------- |
| organisation | Unique organisation namespace of your choosing                      |
| name         | App name, must be unique to each organisation                       |
| version      | App version, unique to each app. Must be increased for each publish |
| server       | Address to the asset server                                         |
| js           | Configuration for JavaScript assets                                 |
| css          | Configuration for CSS assets                                        |
| import-map   | Specify import maps to be used to map bare imports during bundling  |

##### organisation

All asset uploads are scoped to an `organisation`. You may choose any organisation name that is not already taken or that you already belong to. Organisation names may contain any letters or numbers as well as the `-` and `_` characters.

_Example_

```json
{
    "organisation": "finn"
}
```

##### name

All asset uploads within each organisation must have a name. When publishing a dependency from npm the name will be the package name taken from the module's `package.json` file. When publishing the assets for your app, the `name` field of your project's `assets.json` file is used.
Names may contain any letters or numbers as well as the `-` and `_` characters.

```json
{
    "name": "my-awesome-app"
}
```

##### version

All asset uploads are unique by organisation, name and version. It is not possible to republish the same app with the same version in the same organisation. In order to publish a new version of an asset, the version number must first be incremented. When publishing an asset from npm, the version of the package comes from the packages `package.json` version field. When publishing assets for your own app, the version comes from the version specified in `assets.json`. In both cases, versions comply with semver.

The `version` property in `assets.json` starts at `1.0.0` by convention and should be incremented as you see fit either manually or by using the `asset-pipe version major|minor|patch` command.

Either way, when you attempt to republish a package with the same version, publishing will fail and you will need to update the version field before trying again.

```json
{
    "version": "1.0.0"
}
```

##### server

This is the address to the asset server you are using. This might be a locally running version of the asset server (usually `http://localhost:4001`) or an asset server running in production (TBD)

```json
{
    "server": "http://localhost:4001"
}
```

##### js

This field is used to configure bundling and publishing of JavaScript assets. Use `js.input` to configure the location on disk, relative to `assets.json`, where the entrypoint for your JavaScript client side assets are located.

_scripts.js file inside assets folder_

```json
{
    "js": {
        "input": "./assets/scripts.js"
    }
}
```

##### css

This field is used to configure bundling and publishing of CSS assets. Use `css.input` to configure the location on disk, relative to `assets.json`, where the entrypoint for your CSS client side assets are located.

_styles.css file inside assets folder_

```json
{
    "css": {
        "input": "./assets/styles.css"
    }
}
```

##### import-map

This field is used to configure the location of any import map files to be used when creating bundles. The field should be an array and can hold any number of url strings pointing to locations of import-map files that will be downloaded and merged together

_defining a single import map file_

```json
{
    "import-map": ["http://localhost:4001/map/my-import-map/1.0.0"]
}
```

### version

This command updates the `version` field of an `assets.json` file in the current directory based on the argument given (`major`, `minor`, `patch`).

The command takes the form:

```sh
asset-pipe version major|minor|patch [optional arguments]
```

**Examples**

_Increase the version's semver major by 1_

```bash
asset-pipe version major
```

_Increase the version's semver minor by 1_

```bash
asset-pipe version minor
```

_Increase the version's semver patch by 1_

```bash
asset-pipe version patch
```

### publish

This command publishes the app's client side assets to the asset server based on the values in an `assets.json` file in the current directory.

The command takes the form:

```sh
asset-pipe publish [optional arguments]
```

**Example**

_Publishing app assets to server_

```bash
asset-pipe publish
```

#### dependency

This command will download the specified (by name and version) package from NPM, create a bundle with it and then publish it to the asset server. The resulting bundle will be in esm module format, converting from common js if needed.

_Note_ The arguments `server`, `organisation` and `import-map` are taken from `assets.json` if such a file is present in the current directory. If not, you will need to specify these values with the command line flags `--server`, `--org` and `--map`.

The command takes the form:

```sh
asset-pipe dependency [optional arguments] <name> <version>
```

**Example**

_Publishing a dependency from npm_

```bash
asset-pipe dependency lit-html 1.1.2
# asset-pipe dependency --server http://localhost:4001 --org finn --map http://localhost:4001/finn/map/my-import-map/1.0.0 lit-html 1.1.2
```

### alias

This command creates a semver alias for a given published bundle. Creating aliases allows for more flexible referencing of published bundles. You can update an alias to point to the latest version of a bundle without needing to update every client that makes use of your bundle.

_Note_ The arguments `server` and `organisation` are taken from `assets.json` if such a file is present in the current directory. If not, you will need to specify these values with the command line flags `--server` and `--org`.

The command takes the form:

```sh
asset-pipe alias [optional arguments] <name> <version> <alias>
```

_Example_

Running the following command...

```bash
asset-pipe alias lit-html 1.1.2 1
# asset-pipe alias --server http://localhost:4001 --org finn lit-html 1.1.2 1
```

...will create or update the `lit-html` alias `1` to point at `lit-html` version `1.1.2`

### map

This command uploads an import map json file you have created locally to the server. You must upload the file with a `name` and a `version` and the file must be of the form:

```json
{
    "imports": {
        "<dependency name 1>": "url to dependency",
        "<dependency name 2>": "url to dependency"
    }
}
```

_Note_ The arguments `server` and `organisation` are taken from `assets.json` if such a file is present in the current directory. If not, you will need to specify these values with the command line flags `--server` and `--org`.

The command takes the form:

```sh
asset-pipe map [optional arguments] <name> <version> <path to file>
```

```bash
asset-pipe map my-import-map 1.0.0 ./import-map.json
# asset-pipe map --server http://localhost:4001 --org finn my-import-map 1.0.0 ./import-map.json
```

## Programmatic Usage

All of the commands described above can be used programmatically by importing this package. Each command and its programmatic usage is given below.

### init

```js
const cli = require('@asset-pipe/cli');
const result = await new cli.Init(options).run();
```

#### options

| name    | description                           | type   | default         | required |
| ------- | ------------------------------------- | ------ | --------------- | -------- |
| logger  | log4j compliant logger object         | object | `null`          | no       |
| cwd     | path to current working directory     | string | `process.cwd()` | no       |
| org     | organisation name                     | string | `''`            | no       |
| name    | app name                              | string | `''`            | no       |
| version | app version                           | string | `'1.0.0'`       | no       |
| server  | URL to asset server                   | string | `''`            | no       |
| js      | path to client side script entrypoint | string | `''`            | no       |
| css     | path to client side style entrypoint  | string | `''`            | no       |

### version

```js
const cli = require('@asset-pipe/cli');
const result = await new cli.Version(options).run();
```

#### options

| name   | description                           | type   | default         | options                   | required |
| ------ | ------------------------------------- | ------ | --------------- | ------------------------- | -------- |
| logger | log4j compliant logger object         | object | `null`          |                           | no       |
| cwd    | path to current working directory     | string | `process.cwd()` |                           | no       |
| level  | semver level to bump version field by | string |                 | `major`, `minor`, `patch` | yes      |

### publish

```js
const cli = require('@asset-pipe/cli');
const result = await new cli.publish.App(options).run();
```

#### options

| name    | description                           | type     | default         | required |
| ------- | ------------------------------------- | -------- | --------------- | -------- |
| logger  | log4j compliant logger object         | object   | `null`          | no       |
| cwd     | path to current working directory     | string   | `process.cwd()` | no       |
| org     | organisation name                     | string   |                 | yes      |
| name    | app name                              | string   |                 | yes      |
| version | app version                           | string   |                 | yes      |
| server  | URL to asset server                   | string   |                 | yes      |
| js      | path to client side script entrypoint | string   |                 | yes      |
| css     | path to client side style entrypoint  | string   |                 | yes      |
| map     | array of urls of import map files     | string[] | `[]`            | no       |
| dryRun  | exit early and print results          | boolean  | false           | no       |

### dependency

```js
const cli = require('@asset-pipe/cli');
const result = await new cli.publish.Dependency(options).run();
```

#### options

| name    | description                       | type     | default         | required |
| ------- | --------------------------------- | -------- | --------------- | -------- |
| logger  | log4j compliant logger object     | object   | `null`          | no       |
| cwd     | path to current working directory | string   | `process.cwd()` | no       |
| org     | organisation name                 | string   |                 | yes      |
| name    | app name                          | string   |                 | yes      |
| version | app version                       | string   |                 | yes      |
| server  | URL to asset server               | string   |                 | yes      |
| map     | array of urls of import map files | string[] | `[]`            | no       |
| dryRun  | exit early and print results      | boolean  | false           | no       |

### map

```js
const cli = require('@asset-pipe/cli');
const result = await new cli.publish.Map(options).run();
```

#### options

| name    | description                            | type   | default         | required |
| ------- | -------------------------------------- | ------ | --------------- | -------- |
| logger  | log4j compliant logger object          | object | `null`          | no       |
| cwd     | path to current working directory      | string | `process.cwd()` | no       |
| org     | organisation name                      | string |                 | yes      |
| name    | app name                               | string |                 | yes      |
| version | app version                            | string |                 | yes      |
| server  | URL to asset server                    | string |                 | yes      |
| file    | path to import map file to be uploaded | string |                 | yes      |

### alias

```js
const cli = require('@asset-pipe/cli');
const result = await new cli.Alias(options).run();
```

#### options

| name    | description                             | type   | default | choices      | required |
| ------- | --------------------------------------- | ------ | ------- | ------------ | -------- |
| logger  | log4j compliant logger object           | object | `null`  |              | no       |
| server  | URL to asset server                     | string |         |              | yes      |
| org     | organisation name                       | string |         |              | yes      |
| type    | type of resource to alias               | string |         | `pkg`, `map` | yes      |
| name    | app name                                | string |         |              | yes      |
| version | app version                             | string |         |              | yes      |
| alias   | major number of a semver version number | string |         |              | yes      |
