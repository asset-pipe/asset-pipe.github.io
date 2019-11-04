# Creating Aliases

Aliasing allows you to tag specific published versions of modules with a more general tag or version that you are also able to centrally change and control as needed.

The benefit of this is that you can alias a specific version of a dependency and then update that alias overtime as you publish new versions of the dependency and have all dependents immediately receive the change.

## Example use case

Taking the previous example 1 step further, before we saw that we could globally publish a specific version of lodash, in this case `4.17.15`.

We can now set a major semver alias for this version:

```sh
asset-pipe alias lodash 4.15.15 4
```

We can now change our import statement to:

```js
import lodash from `http://<asset server url>/<organisation>/pkg/lodash/v4`;
```

and everything will work as before.

When a new version of lodash comes out, we can create a global dependency for it as before:

```sh
asset-pipe dependency lodash 4.17.16
```

And then create a major semver alias for the new version like so:

```sh
asset-pipe alias lodash 4.15.16 4
```

In this way, no client side code will need to be updated to reflect this change and it is considerably easier for multiple teams to stay in sync, using the same global shared dependency
