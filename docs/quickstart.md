# Quickstart

## Step 1.

Generate an assets.json file in the current directory

```sh
asset-pipe init
```

Fill in the generated `assets.json` file with the necessary details.

For the `server` property, if you are using a locally running asset server
the server property will likely be `http://localhost:4001`

Set the `js.input` and `css.input` properties of `assets.json` with paths to client side
asset files in your project relative to the `assets.json` file.
Eg. if you have a `scripts.js` file in an assets directory, the `js.input` value will be `assets/scripts.js`

## Step 2

Run publish to publish your assets to the server

```sh
asset-pipe publish
```

For subsequent publishes, you will need to version your `asset.json` file before publishing again
since each publish version is immutable. You can do this by editing `assets.json` and setting a
new previously unpublished `version`. You should adhere to `semver` using a version number that makes sense. The cli can help you with this.

Eg. To bump the version from `1.0.0` to `1.0.1` you can use the version patch command like so:

```sh
asset-pipe version patch
```
