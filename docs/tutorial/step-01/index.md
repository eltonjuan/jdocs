---
title: Local Environment
template: doc.hbs
url: /docs/tutorial/step-01/
---
# Step 01 - Local Environment

We're now ready to build our first site using Juno. In this step, we're going to get familiar with some key concepts of Juno, as well as learn how to run our site in the browser. 

In the `juno-tutorial` directory, run the following command: 

```bash
$ git checkout -f step-01
```

This will reset your workspace to step 01 of the juno-tutorial app. You will repeat this command with every subsequent step in this tutorial. 

<visual><break></break></visual>

## Install Dependencies and Build

Now that we've checked out the correct branch, let's install our dependencies by running: 

```bash
$ npm install --verbose
```

This will install all of the necessary dependencies to boot our tutorial site. (Note: the `--verbose` flag is not necessary, it just tells npm to output additional logs which may be helpful in troubleshooting connection issues.)

Once the `npm install` command has completed, you can boot the tutorial site by executing the following command: 

```bash
$ juno serve
```

The `serve` command is a feature of the Juno CLI. The `serve` command will pull in all of our vendor dependencies (including the Juno core scripts) and boot a local server with a watcher that will watch source files for changes and automatically refresh the page. Behind the scenes, the `serve` command invokes the gulp `watch` task with the `-n` flag, which skips the uglify step when bundling our script dependencies. 

If all goes well, you should see the following message: 

```bash
17:06:53.745Z  INFO juno: Using site juno-site.
17:06:54.605Z  INFO juno: juno-site listening at http://localhost:8080 [development]
```

Now, point your browser of choice at `http://localhost:8080` and you should see an example page that has an example header and footer as well as a sample content block that says "Content". 

All good? Head onward to the next step, [Step 02 - Shells and Routing](/docs/tutorial/step-02/). 
















