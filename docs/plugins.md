---
prev: ./env-reference.html
next: ./ui.html
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
The name is the name of the plugin when registered. The version is the version set when registered. Dependencies are other plugin dependencies that are required for the plugin to run.

> Please note: as of right now, dependencies are not working, so this field won't do anything until the issues with it are resolved.

There will need to be three directories created, if you plan on using them:

admin/ - For all your admin logic

controllers/ - For your controllers

models/ - For your models

## Creating a Model

By creating a model, you are creating all your database logic for the application. We use Hapi Schwifty, which uses the Objection ORM to to make working with the database easier. An example of a model file:

```js
const Joi = require('joi')
const Schwifty = require('schwifty')

module.exports = async (server) => {
  const knex = server.knex()

  server.schwifty(
    class Post extends Schwifty.Model {
      static get tableName() {
        return 'Post'
      }

      static get joiSchema() {
        return Joi.object({
          id: Joi.number(),
        })
      }
    }
  )

  await knex.schema.hasTable('Post').then(async (exists) => {
    if (!exists) {
      await knex.schema
        .createTable('Post', (table) => {
          table.increments('id').primary()
        })
        .then(() => {
          console.log('Successfully created Post table.')
        })
        .catch((e) => {
          console.error('Error when trying to create Post table', e)
        })
    }
  })
}
```

This example creates a simple Post model (and Post table) that has an ID field only.  We also validate the data with the Joi module to make sure they are the data we expect. This is a basic example, and if you want to learn more,  you can check out the [Schwifty documentation](https://github.com/hapipal/schwifty). You can also view an example in the [creating your first application](/guide.html#creating-your-first-application) section of the site.

## Creating a Controller

By creating a controller, you are creating all the handler logic for the application. This means that your return values from these are what data gets returned as part of your API's, or views. The following is an extremely simple Post Controller:

```js
exports.PostController = {
  async get(request, h) {
    return 'Hello World!'
  }
}
```

This simple method "get" within the controller returns a simple string of 'Hello World!'. So if you were able to pass this into a route, that would be the returned value. Controllers help keep the logic of the handling of a route separated into it's own file, keeping the code much cleaner.

Feel free to view any of the [Hapi Docs](https://hapi.dev) to look at how things can work within the controllers.

## Creating a route file

You will also need a routes.js file similar to the following, in the root of the plugin, which will give you access to everything within the application:

```js
exports.routes = (server) => {
  return {
    // Everything under this route group with require authentication
    // By default, available by path "/admin/api"
    admin: [],
    // By default, available by path "/api"
    api: [],
    // By default, available by path "/"
    web: []
  }
}
```

Admin routes fall under routes that are hit by admin to get data. With these routes, you are required to be authenticated to be able to access them.

API routes fall under a specific api path, to be consumed by a front end, or used for APIs

The Web route falls under the root path of the application. This can used to create front ends, or return html.

If you want to add a route, you can do the following to one the array sections above:

```js
{
  method: 'GET',
  path: '/get',
  handler: (request, h) => {
    return 'Hello World!'
  }
}
```

This would return a basic "Hello World" string. But, to create a separation of concerns, we can actually use the controller methods as the handlers. So we only need to inport on controller that is available within the application:

```js
const { PostController } = server.controllers
```

Now we can just use the handler from there:

```js
{
  method: 'GET',
  path: '/get',
  handler: PostController.get
}
```
>NOTE: Whether you use controller methods, or create your own callback, you must always return a value. Otherwise, you will receive a 500 error when you go to the route.

If you would like to learn more, you can view the Hapi documentation on routing and routing methods [here](https://hapi.dev/tutorials/routing/)

## Using the CLI

While everything above is a general If you create a new plugin via the CLI, much of the plugin scaffolding is created for you. You only need to run the following command:

```sh
node circulate create:plugin <plugin name>
```
You will get the following prompts:

```sh
? What is the name of your plugin? 
? What version would you like to start your plugin? 
? Your Model name?
? Your controller name?
? Your Admin component name?
```

If you accept all the defaults, the structure is as follows:

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

> NOTE: This is the structure you need for a plugin, regardless of using the CLI, or creating your own, otherwise, CirculateJS won't properly auto load Models, Views, or Controllers.

> NOTE: It is possible to create your own directory structure if you choose to, but you will need to properly import the javascript files that exist outside of the directories using CommonJS (i.e., const file = require('./file'))

## Admin

One of the most powerful things that can be done is creating interfaces in admin that sit behind authentication. You can create anything you want within Admin.

### Creating Routes In Admin

Here is an example of creating an admin page with the adminRoutes.js file within the admin directory of the plugin:

```js
export default [
  {
    path: '/post',
    menu: 'Post',
    component: () => import(/* webpackChunkName: "post" */ './Post.vue')
  }
]
```

So, what is the difference between creating routes for the admin section with the the "routes.js" file, and creating something in "adminRoutes.js" file? The "routes.js" file handles just the api data, where the "adminRoutes.js" is specifically for creating a User Interface within admin. So in the above example, you can go the following URL, and you would get the Post.vue file to load as part of the interface:

http://localhost:3000/admin/post

The "menu" item creates a menu item in Admin on the left handed side menu.

So what is the arrow function doing on the component value? This with dynamically load the component when you click on the menu item.  This makes load times on initial load much faster. You can learn more about it [here](https://vuejs.org/v2/guide/components-dynamic-async.html#Async-Components). The "/* webpackChunkName: "post" */" section will generate everything under a single "post" js file when it generates the production build. You can change this to anything you want.

> NOTE: The Webpack Chunk Name can be changed to any value, but it is recommended to either keep the names within the plugin score name, or create a name for each route. This will keep the javascript slimmer in the end result.


### Icons

The CirculateJS Admin makes use of Tabler Icons. These can be used in anything within [CirculateJS UI](/ui.html). This can also be used to add icons to the menu items. An example of that is in the following "adminRoutes.js" file. You just need to add an icon value, and the name of the icon:

```js
icon: 'square-plus'
```

If you would like to use them instead of the default one, you can view them on [Github](https://github.com/tabler/tabler-icons).

You can also view [tablericons.com](https://tablericons.com) for a quick search of the icons.

### UI And Beyond

CirculateJS UI is available for the application, and you can learn more about it [here](/ui.html), but it is not required to be used. You can create any look and feel you want within admin. We just offer some predefined ways to keep the same look and feel for you, and give you a head start on creating some of the UI in admin.


### Async Data

Async data can be used globally within admin under the method "$http". An example of a get method is as follows:

```js
getPost() {
  this.$http
    .get('/admin/api/post')
    .then((response) => {
      console.log(response.data)
    })
    .catch((error) => {
      console.error(error)
    })
}
```

If you need more information on the Axios library, you can look at the documentation [here](https://github.com/axios/axios)
