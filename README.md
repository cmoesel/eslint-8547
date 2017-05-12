This project was bootstrapped with [Create React App](https://github.com/facebookincubator/create-react-app).

This project exists only to demonstrate the bug reported at:
* https://github.com/eslint/eslint/issues/8547
* https://github.com/yarnpkg/yarn/issues/3332

This project was created using the `create-react-app` command, and eslint was configured using the `eslint --init` command, specifying the AirBnB extension.  After that, the `yarn` command was run to update `yarn.lock` with changes that `eslint --init` made to the `package.json` file.  Finally, a few minor tweaks to dependency versions were made to resolve some issues w/ incompatible versions installed by `eslint --init`.  See the commits for the play-by-play.

# Versions

Operating System: macOS Sierra 10.12.4
Node: v6.6.0
NPM: 3.10.3
Yarn: 0.23.4

# Data

If you're a npm or yarn guru, maybe you just want to see the differences between the resulting `node_modules` folders.  OK!  Check out the [npm_data](npm_data) and [yarn_data](yarn_data) folders.  There I've checked in the results from `npm ls` and `yarn list`, as well as the entire contents of the respective `node_modules` folders.

# Reproducing

If you like tinkering, or don't believe me and want to see it for yourself (!), the instructions below describe how to reproduce this problem.

## Clone this repository

You're on Github.  Hopefully you know how to do this.

## NPM Install and Run ESLint

**Run `npm install`**

This will populate your node_modules folder with all dependencies from package.json.

**Run `node_modules/.bin/eslint .`**

Note that there are some eslint errors reported back, but these are _valid_ violations that were correctly detected by eslint:

```
/Users/cmoesel/dev/eslint_8547/eslint-8547/src/App.js
  5:1  error  Component should be written as a pure function  react/prefer-stateless-function
  8:7  error  JSX not allowed in files with extension '.js'   react/jsx-filename-extension

/Users/cmoesel/dev/eslint_8547/eslint-8547/src/App.test.js
  5:1   error  'it' is not defined                            no-undef
  6:15  error  'document' is not defined                      no-undef
  7:19  error  JSX not allowed in files with extension '.js'  react/jsx-filename-extension

/Users/cmoesel/dev/eslint_8547/eslint-8547/src/index.js
  7:3   error  JSX not allowed in files with extension '.js'  react/jsx-filename-extension
  8:3   error  'document' is not defined                      no-undef
  8:34  error  Missing trailing comma                         comma-dangle

✖ 8 problems (8 errors, 0 warnings)
```

## Clean node_modules, Yarn Install and Run ESLint

**Run `rm -rf node_modules`**

We need to clear out the node_modules since now we'll try having yarn install them.

**Run `yarn`**

(Of course, you'll need to install yarn if you don't have it.)

This will populate your node_modules folder with all dependencies from yarn.lock (which was generated from package.json).

**Run `node_modules/.bin/eslint .`**

This will report many more errors than when we ran it after using npm to install the dependencies.  The main errors to note are all the ones about _missing rules_.

```
/Users/cmoesel/dev/eslint_8547/eslint-8547/src/App.js
  1:1  error  Definition for rule 'import/no-named-default' was not found      import/no-named-default
  1:1  error  Definition for rule 'react/jsx-tag-spacing' was not found        react/jsx-tag-spacing
  1:1  error  Definition for rule 'react/no-array-index-key' was not found     react/no-array-index-key
  1:1  error  Definition for rule 'react/require-default-props' was not found  react/require-default-props
  5:1  error  Component should be written as a pure function                   react/prefer-stateless-function
  8:7  error  JSX not allowed in files with extension '.js'                    react/jsx-filename-extension

/Users/cmoesel/dev/eslint_8547/eslint-8547/src/App.test.js
  1:1   error  Definition for rule 'import/no-named-default' was not found      import/no-named-default
  1:1   error  Definition for rule 'react/jsx-tag-spacing' was not found        react/jsx-tag-spacing
  1:1   error  Definition for rule 'react/no-array-index-key' was not found     react/no-array-index-key
  1:1   error  Definition for rule 'react/require-default-props' was not found  react/require-default-props
  5:1   error  'it' is not defined                                              no-undef
  6:15  error  'document' is not defined                                        no-undef
  7:19  error  JSX not allowed in files with extension '.js'                    react/jsx-filename-extension

/Users/cmoesel/dev/eslint_8547/eslint-8547/src/index.js
  1:1   error  Definition for rule 'import/no-named-default' was not found      import/no-named-default
  1:1   error  Definition for rule 'react/jsx-tag-spacing' was not found        react/jsx-tag-spacing
  1:1   error  Definition for rule 'react/no-array-index-key' was not found     react/no-array-index-key
  1:1   error  Definition for rule 'react/require-default-props' was not found  react/require-default-props
  7:3   error  JSX not allowed in files with extension '.js'                    react/jsx-filename-extension
  8:3   error  'document' is not defined                                        no-undef
  8:34  error  Missing trailing comma                                           comma-dangle

✖ 20 problems (20 errors, 0 warnings)
```

# Conclusion

There is some difference in the way that yarn installs dependencies and npm installs dependencies that affects how eslint loads the configuration packages.  The question is, why does this happen, and is it a yarn issue or an eslint issue?