---
title: Getting Started
template: doc.hbs
url: /docs/getting-started/
---

# Getting Started

To get started with Juno, you first need a few prerequisites. Juno is built on top of the [Node.js](http://nodejs.org/) platform, and all Juno projects are built with [gulp](http://gulpjs.com/).

## Node.js

To install Node.js, [visit their site](http://nodejs.org/) and follow the installation instructions for your platform. To validate that Node.js is installed correctly, you can check that the CLI correctly prints a version.

```bash
# Ensure that Node.js is installed.
$ node -v
```

### Gulp

Once Node.js is installed, you can install the `gulp` CLI with `npm`. Open up your terminal and run the following command.

```bash
# Install the gulp npm module globally.
$ npm install -g gulp
```

To validate that gulp is installed correctly, you can check that the CLI correctly prints a version.

```bash
# Ensure that gulp is installed.
$ gulp -v
```

## Artifactory

In order to access the internal packages on the Nike network, you need to set up access to Nike's internal npm reposity hosted on Artifactory. First, you'll need to set your environment's proxy settings. You can do this by adding the following lines to your bash profile.

```bash
NIKE_PROXY=http://connsvr.nike.com:8080
NIKE_NOPROXY=stash.nikedev.com,artifactory.nike.com,dcit.nike.com,dcit.nikedev.com,localhost,127.0.0.1
# Allow command line apps (node/npm/etc) to access outside servers
export HTTP_PROXY=$NIKE_PROXY
export http_proxy=$HTTP_PROXY
export HTTPS_PROXY=$NIKE_PROXY
export https_proxy=$HTTPS_PROXY
# Servers to not proxy
export NO_PROXY=$NIKE_NOPROXY
export no_proxy=$NO_PROXY
```

Artifactory requires the latest version of `npm`, which you can install with the following command.

```bash
$ sudo npm install npm -g
```

You may need to clear out any current npm proxy settings, especially if you're coming from another team at Nike. Delete your current settings file...

```bash
$ rm -rf ~/.npmrc
```

OR

Remove the `http_proxy` and `https_proxy` settings from `~/.npmrc` manually.

You can now set your `npm` configuration to use artifactory instead of the default registry.

```bash
$ npm config set registry http://artifactory.nike.com/artifactory/api/npm/npm-nike
```

## Juno CLI

The easiest way to get up and running with Juno is to leverage the Juno CLI, which can be installed by running the following command in your terminal.

```bash
# Install juno-cli globally.
$ npm install -g juno-cli
```

You can now scaffold a new site with the Juno CLI.

```bash
# Scaffold a new site.
$ juno new site
```

The Juno CLI can be used to perform helpful tasks such as scaffolding a new Juno site, automatically upgrading your site to a new version of Juno, creating new View Components, and creating unit test scaffolds. See the [Juno CLI](/docs/juno-cli/) documentation for more details, or [click here](/docs/juno-cli#new-site) to see how to create a new site.

### Other Developer Tools

In addition to the Juno CLI, there are also Sublime Text packages and a Chrome Extension available to speed up Juno experience development.
Visit the [Developer Tools](/docs/dev-tools) for more information.

## Next Steps

When you're ready to learn more, head over to the [tutorial](/docs/tutorial)!
