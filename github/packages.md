# GitHub Package Registry NPM

https://help.github.com/en/articles/configuring-npm-for-use-with-github-package-registry

Set your package name to the rigth scope in `package.json`

```
...
  "name": "@hyperhooks/hyperhooks",
...
```

Create an access token

https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line

You must use a personal access token with the `read:packages` and `write:packages` scopes to publish and delete public packages in the GitHub Package Registry with npm.

Login to GitHub packages

    npm login --registry=https://npm.pkg.github.com --scope=@hyperhooks

Publish

    npm publish

