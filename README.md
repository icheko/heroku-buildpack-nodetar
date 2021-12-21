# heroku-buildpack-tarmodules

This buildpack allows you to install a node package using a tar file included in source. This is used in cases where you dont have direct access to download the package from an npm repo.

### Prereqs

The tar file must be prefixed with the same name as the package. In the example below, `my-package-1.0.0.tgz` will be expecting the package name `my-package` in the `package.json` file. Tar files must end with extensions (.tar, .tar.gz, .tgz).

#### source

    -> /
       - my-package-1.0.0.tgz
       - package.json

#### package.json

```
{
  "name": "tarmodules-app-example",
  "version": "1.0.0",
  ...
  "dependencies": {
    ...
    "my-package": "^1.0.0"
  }
}
```
### Build Process
When this buildpack runs, it will do the following for each tar file:
1. Uncompress the tar into `tar_modules` and the folder name will be the same as the tar file without extension. For example, a tar called `my-package-1.0.0.tgz` will be uncompressed to `tar_modules/my-package-1.0.0`.
2. The package defined in `package.json` will be updated to install from the `tar_modules` location. For example, the package definition for `my-package` will be updated from `"my-package": "^1.0.0"` to `"my-package": "file:tar_modules/my-package-1.0.0"`.

## Using the buildpack
```
heroku buildpacks:add [this-url].git --index 1 --app myapp
```
Set this buildpack as the first one before the `heroku/nodejs` buildpack.

### Caching

If you have node_module cache enabled, add `tar_modules` to the `cacheDirectories` in `package.json`

```
{
  "name": "tarmodules-app-example",
  "version": "1.0.0",
  ...
  "cacheDirectories": [
    "tar_modules"
  ],
  "dependencies": {
    ...
    "my-package": "^1.0.0"
  }
}
```
