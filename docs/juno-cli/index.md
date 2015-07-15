---
title: Juno CLI
template: doc.hbs
url: /docs/juno-cli/
---

# Juno CLI

The Juno CLI is a command line interface for developing Juno sites. It can be used to perform helpful tasks such as scaffolding a new Juno site, automatically upgrading your site to a new version of Juno, and creating new ViewComponents and Services.

The Juno CLI source code can be found on <a href="http://stash.nikedev.com/projects/JUNO/repos/juno-cli" target="_blank">Stash</a>.

<visual><break></break></visual>

## Installation

The CLI is built on Node.js, and can be installed with npm as a global package. You can install the CLI by running the following in your terminal. Depending on your node installation, you may need to run the installation with `sudo`.

```bash
# Install juno-cli globally
$ npm install -g juno-cli
```

This will make the Juno CLI available under `juno`. To ensure that the CLI was installed properly, run `juno --help` and make sure that it prints out the help screen.

<visual><break></break></visual>

## Scaffolding

One of the primary uses for the Juno CLI is to scaffold new sites, ViewComponents, and Services. A list of available scaffolding commands can be seen by running `juno new`.

<a id="new-site"></a>
### Sites

To create a new juno site, simply run `juno new site`. The first question it will ask is what version of Juno you would like to use. All major Juno releases are available to use, and the appropriate build and configuration scripts will be included for the specific version. 

```bash
# Scaffold out a new Juno site
$ juno new site
? What version of Juno would you like to use? (Use arrow keys)
❯ 0.8.0 
  0.7.1 
```

Once you've selected the version of Juno you would like to use, it will ask for the name of the site. This name is also automatically included in your site's `package.json`. The default name is "juno-site".

```bash
$ juno new site
? What version of Juno would you like to use? 0.8.0
? What would you like to name the site? (juno-site) |
```

The last step is to select the folder that the site will be generated in. By default, a new folder with the site name is created in your current working directory.

```bash
$ juno new site
? What version of Juno would you like to use? 0.8.0
? What would you like to name the site? juno-site
? What folder should we scaffold this site in? (/cwd/juno-site) |
```

And just like that, you have scaffolded a complete Juno site! The CLI will automatically install all npm dependencies for you. You can now `cd` into your site directory and run `juno serve` to start the site.

```bash
Site juno-site created.

$ cd juno-site
$ juno serve
```

### ViewComponents and Services

To scaffold out a ViewComponent or Service, you can run `juno new component` or `juno new service` in a directory containing a Juno site. You first must give it a name. _Remember that names for ViewComponents and Services must be PascalCase._

```bash
$ juno new component
? What would you like to name the Service? |
```

You will then be able to specify a folder for the ViewComponent or Service to be placed in.

```bash
$ juno new component
? What would you like to name the Service? MyService
? What folder should the Service directory exist in? (excluding `services/`) |
```

After confirming the file path, the files will be created.

<visual><break></break></visual>

## Upgrading

You can use the CLI to upgrade your site to a new version of Juno. This will update all of the build scripts, install any new npm dependencies, and update the version of Juno your site points at. To start the upgrade process, you can run `juno upgrade`.

```bash
# Check to see if an upgrade is available.
$ juno upgrade
```

If an upgrade is available, the CLI will tell you what version you will be upgrading to, and ask you if you would like to perform the upgrade. Once an upgrade is complete, the CLI will perform an npm install and generate an upgrade report.

_Note that modifying files containing a warning in the header may result in conflicts during upgrade process._

### Reports

After an upgrade is complete, an upgrade report is generated. This is used to identify sections of your experience code that are not compatible with the new version of Juno, without requiring you to start the site and wait for errors.

While the report is run automatically at the end of an upgrade, you can manually run it again at any time.

```bash
# Run the upgrade report manually.
$ juno report
```
