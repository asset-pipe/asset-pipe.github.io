# Using import maps to map "bare imports"

Import maps are [an emerging standard](https://github.com/WICG/import-maps) and a way to map "bare imports" such as `foo` in the import statement `import { bar, baz } from 'foo'` to modules to be loaded. In Asset Pipe, we provide a way to upload import map files and to specify them for use in bundling. Doing so allows you to specify, across an organisation, a common set of shared modules whether they be `react` or `lit-html` or whatever.

Making use of import maps is as follows.

1. Define an import map json file
2. Use the asset pipe CLI to upload the import map to the server
3. Specify the URL to your import map file(s) in your `assets.json` file
4. Use the `publish` commands, your import maps will be used to map bare imports in your code to the URLs you have defined in your import maps

## Import maps, an example

Given the following import map file `import-map.json`

```json
{
    "imports": {
        "lit-html": "http://localhost:4001/finn/pkg/lit-html/v1/index.js",
        "lodash": "http://localhost:4001/finn/pkg/lodash/v4/index.js"
    }
}
```

The following command will upload the import map file `./import-map.json` in the current directory using the name `my-import-map` and the version `1.0.0`

```sh
asset-pipe --org finn map my-import-map 1.0.0 ./import-map.json
```

Given the following line now added to `assets.json`

```json
{
    "import-map": ["http://localhost:4001/finn/map/my-import-map/1.0.0"]
}
```

When we run `asset-pipe publish` any "bare imports" refering to either `lit-html` or `lodash` will be mapped to the URLs in our map.

In this way, you can control which version of `react` or `lit-html` or `lodash` all the apps in your organisation are using. In combination with package `alias` URLs you have a powerful way to manage key shared dependencies for your apps in production without the need to redeploy or rebundle when a new version of a dependency is released.
