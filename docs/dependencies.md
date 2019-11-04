# Managing Dependencies

When you wish to share a version of a module used across an organisation, you can use the `dependency` command to do so.

This feature does the following:

-   converts a module already published to npm to esm
-   makes it available through the asset server

## Example use case

You might decide that all teams across your organisation should use the same version of lodash via a publish url (rather than each team bundling their own version).

To do so you would run:

```sh
asset-pipe dependency lodash 4.17.15
```

After running this, an esm friendly version of lodash will be available at the url:
`http://<asset server url>/<organisation>/pkg/lodash/4.17.15`

It's now possible for each team to reference this globally published module directly in their
own client side code as follows:

```js
import lodash from `http://<asset server url>/<organisation>/pkg/lodash/4.17.15`;
```

This has the benefit that if all teams are referencing lodash in this way, the browser will cache the module the first time it encouters it and all subsequent pages will not need to download it again.
