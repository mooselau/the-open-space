# Intro
Here records my own experiences of using Gitbook, based on several docs online.

# Requirements
> 1.NPM NVM
> 2.Gitbook-cli

# Process
Due to gitbook-cli depends on older version of Node(\<v.14), hence installing NVM is necessary for easily switch different Node version.

## Step 0 Installing NVM
To install NVM, remove existing Node and re-install Node after NVM installation.

```sh
# uninstall existing node if using homebrew
$ brew uninstall node
# install nvm
$ brew install nvm

# use nvm to install latest node version of v17
$ nvm install 17
# install node v14
$ nvm install 14
# install node v12
$ nvm install 12

# switch node version used by NVM
$ nvm use 12
# check current in-using node
$ nvm list
```

## Step 1 Install & Init Gitbook
Find a Github Repo that you want to create Gitbook, run below command to initialize Gitbook Pages:

```sh
# installing gitbook-cli
$ npm install -g gitbook-cli

# init current directory with gitbook-cli
$ gitbook init
```

Please be noted if there is error like "cb.apply is not a function" in polyfills.js, this might also relates to the version, to resolve the issue, we could commented out those error lines like below:

```javascript
// in polyfills.js
// fs.stat = statFix(fs.stat)
// fs.fstat = statFix(fs.fstat)
// fs.lstat = statFix(fs.lstat)
```

And if there is error like below, then it might still has version issue, please double check the node version if it is lower than v13 or v12.

>TypeError [ERR_INVALID_ARG_TYPE]: The "data" argument must be of type string or an instance of Buffer, TypedArray, or DataView. Received an instance of Promise

Afterwards, there should be one SUMMARY.md file created under current directory, it contains the outline of this gitbook. 

## Step 2 Test Locally & Prepare Changes
To view the gitbook page locally, run

```sh
$ gitbook serve
```

Then go to _localhost:4000_ to view the result page.

Also, you could modify your gitbook structure now, after everything is ready, you could build and publish your pages via github.

To pack & commit all necessary files:

```sh
$ gitbook build

# copy everything from _book to current folder
$ cp -R _book/* .

# add everything for commit
$ git add . && git commit -m "commit messages"
# push to a specific branch, e.g. "gitbook"
$ git push
```

## Step 3 Deploy onto Github Repo
Go to repo settings -> Pages, in Github Pages Panel, choose Source to the branch that contains our gitbook pages, then "save". After several minutes, gitbook pages will show in provided address.

An interesting change happens here is Github actually creates workflow in the repo for us, for details please check the link in your github repo: https://github.com/mooselau/the-open-space/actions

