# deploy-script

A script for deploying a project using npm

## Installation

```
git clone https://github.com/mikkelking/deploy-script
cd deploy-script
npm install
```

This is not a complete project, just the main script and a few ancillary files. If you want to use it, you will need to add some dependencies into your package.json, add entries for deployment, set up your version templates etc, and then you will be able to do a deployment using

```
npm run deploy-staging
```

or 

```
npm run deploy-prod
```

In your package.json you will need to add build targets, like this:

```
{
  "name": "deploy-script",
  "private": true,
  "version": "1.0.2",
  "scripts": {
    "deploy-staging": "node scripts/deploy.js staging",
    "deploy-prod": "node scripts/deploy.js prod"
  },

```

Copy the file `scripts/deploy.js` from this repository into your project `scripts` folder.

Modify the script for your own deployment. The example provided is using Meteor up to deploy, but you can just as easily use webpack or anything else.

## Target environments

There is some configuration in the deploy.js script that looks like this:

```
let choices = {
  prod: {
    name: 'production',
    level: 'minor',
    branch: 'master'
  },
  demo: {
    name: 'demo',
    level: 'minor',
    branch: 'develop'
  },
  staging: {
    name: 'staging',
    level: 'patch',
    branch: 'develop'
  },
};
```

This has 3 targets, prod, demo and staging. `level ` determines whether you do a minor or patch bump of the version number. `branch` is to define a required branch to do this deployment. For example if your current branch is feature-something and you try to do a `npm run deploy-prod` it will fail, because you must be on the master branch to do it.

## Templating

The primary purpose of this script is to make the version no available for your app. There is a folder called `templates`, which contains a folder or folders, and a version template file. In this case it's `templates/imports/api/version.js`, which will be templated into a location `app/imports/api/version.js`. If you want a different destination, just changes the folders and filename to suit. It will recurse the folder `templates`, so you can put a file in more than one location if you like. You could put other information in there too if you like, such as build time.

The contents of the version file are up to you as well.

## Version bumping

The config above will bump the `patch` version (eg v1.0.0 => v1.0.1) for staging. For prod it is set to minor, (eg v1.0.5 => v1.1.0)
It also automatically creates a tag for the version, and pushes the tag to your repository.

## Version file conflicts

You may get conflicts in the generated version file after you have done a `deploy-prod`, and later do a `deploy-staging`. The way to avoid this is to make sure you merge `master` back into `develop` when you go back to the `develop` branch.

