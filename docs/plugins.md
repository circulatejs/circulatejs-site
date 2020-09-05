---
prev: /env-reference.html
next: false
---

# Plugins

Plugins are at the heart of a CirculateJS application. When you create a new project, you're given an option to change the plugin path.
The default path is "/plugins". This is where your application logic will exist.

## Creating a new plugin

CirculateJS makes a few assumptions on the path structure when creating a new project, but for the most part, you're able to change the paths to suit your needs.

The main thing you need to create is a manifest file that handles registering the plugin with in application.

```json
{
  "name": "@circulatejs/blog",
  "version": "1.0.0",
  "dependencies": []
}
```
The name is the name of the plugin when registered. The version is the version set when registered. Dependencies are other plugin dependencies that are required for the plugin to run

> Please note: as of right now, dependencies are not working, so this field won't do anything until the issues with it are resolved.

There will need to be three directories created, if you plan on using them:

admin/ - For all your admin logic
controllers/ - For your controllers
models/ - For your models

## Using the CLI

If you create a new plugin via the CLI, much of the plugin scaffolding is created for you. You only need to run the following command:

```sh
node circulate create:plugin <plugin name>
```

The path default structure is as follows:

```
PLUGIN_ROOT
  |- /admin
    |- adminRoutes.js
    |- Component.vue
  |- /controllers
    |- Controller.js
  |- /models
    |- Model.js
  |- manifest.json
  |- routes.js
```
