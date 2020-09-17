---
prev: ./learn-more.html
next: ./env-reference.html
---

# Getting Started

## Prerequisites

You need to have at least Node version 12.0.0 installed on your system. You can check the version with the following command:

```sh
node -v
```

## Installing

To install CirculateJS, you only need to run one command, depending on which package manager you use:

```sh
yarn create circulatejs-app <project-directory>

# Or

npx create-circulatejs-app <project-directory>
```

## Setting Up The Project

After the installer runs, it will ask you a series of questions to setup the project.
Once this is complete, it will install all the dependencies for the project.
You will then be able to start your project with the following command, depending on your package manager of choice:

```sh
yarn start --no-admin

# Or

npm start --no-admin
```

When you start up the server for the first time, you will be required to input a username and password for the first user. From there, the server will start
and you'll be able to log into the admin with the credentials you created.

### Starting The Application Without An Admin

It's also possible to startup the application without generating the admin at all. To do this, run the start command with the following switch:

```sh
yarn start

# Or

npm start
```

There might be a couple of reasons why you don't want this. One reason is that you might not even need the admin for the application, like if you are running your own admin, or have a different way of interacting with the database. Another reason might be, maybe you don't want to generate the admin on everything single start, especially if you need the application to start up faster (the admin remains cached when generated).

### Developing In The Project

If you would like to develop anything for the project, and don't want to have to restart the application every single time
(watch for changes), then your can start the project with the following command, based on your package manager of choice:

```sh
yarn dev

# Or

npm dev
```

## Default Directory Structure

```
APP_ROOT
  |- bootstrap.js
  |- circulate.js
  |- .env
  |- /plugins
  |- package.json
```

You'll notice that by default, the application structure attempts to be as slim as possible. This will hopefully allow you to know exactly where everything is quickly.

> Note that the "/plugins" directory may exist under a different name if you choice a different location when creating the application for the first time. You can also change this directory path at any time. Please view the [env reference](/env-reference.html) for more information.

## The Application Structure

By default, CirculateJS doesn't do much, other than provide an administration interface (/admin by default). The power of the application comes from plugins. The idea behind this is to allow you to create the exact application that you want, without the need for anything extra, or hacking your way through it. This also has the added benefit of keeping your different application logic separated.

So now, how do we go about adding a plugin? You just need to add a directory (preferably as the name of your plugin), and make sure there is a manifest file available for the application to read.

For more detailed information, visit the [plugin page](/plugins.html)

## Creating Your First Application

If you have followed all the instructions above ([installing a new application](/guide.html#installing)), then the next section will walk through how to create a sample application as an example.

> NOTE: Be sure to review the [plugin section](/plugins.html) before you proceed.

Lets create a new blog application.

### Creating A Plugin

First thing you will need to do is create a new blog plugin that CirculateJS core will accept. Run the following command:

```sh
node circulate create:plugin blog
```

Follow the prompts. Call the plugin "blog", and give each Model, Controller, And component the name "Pot". This will now create a blog plugin with the following structure:

```
blog (root)
  |- /admin
    |- adminRoutes.js
    |- Post.vue
  |- /controllers
    |- PostController.js
  |- /models
    |- PostModel.js
  |- manifest.json
  |- routes.js
```

### Adjusting The Model

The next step is adjusting the model. When we generate the model, it has a default id field, but we need to add new fields for our blog application.

Within the "_createTable_" method, we add two new fields:

```js
table.string('title').notNullable()
table.string('body')
```

This will create the new fields as strings in the database, making sure that the title fields can't be null

Optionally, in the "_Joi.options_" method, you can add in some validation for the fields to make sure that they are strings before they hit the database:

```js
title: Joi.string(),
body: Joi.string()
```

### Adjusting Controller Methods

The generator creates default methods, each one depending on the use case. In our case, we want to add to the "post", "get", and "find" methods.

We have access to all Hapi methods that we need in here. You can view the Hapi docs here for reference.

In the "post" controller method, add the following code:

```js
// Get the model
const { Post } = request.server.models()

// Get the payload values
const { title, body } = request.payload

// Add new post to the database
await Promise.all([
  Post.query().insert({
    title,
    body,
  }),
])

// Return the response for adding the new post
return await h.response(`Post "${title}" created`)
```

In the "get" controller method, add the following code:

```js
// Get the model
const { Post } = request.server.models()

// Query for all posts
const posts = await Post.query().select('id', 'title', 'body')

// Return all posts
return await h.response({ posts })
```

In the "find" controller method, add the following code:

```js
// Get the model
const { Post } = request.server.models()

// Get the id from the params
const id = request.params.id

// Get the queried post from the id in the request
const post = await Post.query().findById(id).select('title', 'body')

// Return the single post
return await h.response({ post })
```

Now we have created our controller methods, but how do we use them?

### Adding The Routes

To get access to the controller methods, we are going to need to create routes for them.

Open the "routes.js" file and add the controller to get access to the methods:

```js
const { PostController } = server.controllers
```

You'll want to add the post within the "admin" method of the routes file. This will make sure that any post is behind authentication:

```js
admin: [
  {
    method: 'POST',
    path: '/post/add',
    handler: PostController.post
  }
],
```

You'll want to make sure everything else exists within the "api" method:

```js
api: [
  {
    method: 'GET',
    path: '/posts',
    handler: PostController.get
  },
  {
    method: 'GET',
    path: '/posts/{id}',
    handler: PostController.find
  }
],
```

> NOTE: Make sure the \${id} is the same as the id param within the controller.

We have the basic scaffold in place now for creating a basic blog. Now we need to create a way to add blog posts.

### Create An Admin Component

If you notice the "/admin" directory, there are a few files that were created when you generated the plugin. Lets look at the "adminRoutes.js" file, so we can understand the differences between that and the "route.js" file at the root on the plugin.

```js
export default [
  {
    path: '/post',
    menu: 'Post',
    component: () => import(/* webpackChunkName: "post" */ './Post.vue'),
  },
]
```

This is the main file that generates the admin path and interface to be used within the admin. This file generates a the Vue component and adds the path within admin. The menu item with create a new menu item on the left hand side menu.

Now if you were the start the application right now, and log into admin, and go to the post page, you'll notice something like this:

(image)

This is a default admin page that is generated when you create a plugin. We will want to update it to be able to add new posts.

Change the pages Vue code to the following:

```vue
<template>
  <div>
    <h1 class="heading">Create New Post</h1>
    <form @submit.prevent="submitPost">
      <c-input
        v-model.trim="post.title"
        type="text"
        placeholder="title"
      ></c-input>
      <c-rich-text v-model="post.body"></c-rich-text>
      <div class="inline-flex">
        <c-button type="submit" class="mr-2">
          <c-icon name="plus" class="inline"></c-icon>
          Add
        </c-button>
      </div>
    </form>
  </div>
</template>

<script>
import { cInput, cButton, cIcon, cRichText } from '@circulatejs/ui'
export default {
  name: 'Post',
  components: {
    cInput,
    cButton,
    cIcon,
    cRichText,
  },
  data() {
    return {
      post: {
        title: '',
        body: '',
      },
    }
  },
  methods: {
    submitPost() {
      this.$http
        .post('/admin/api/post/add', this.post)
        .then((response) => {
          this.post.title = ''
          this.post.body = ''
        })
        .catch((error) => {
          console.error(error.response)
        })
    },
  },
}
</script>
```

There are a few things of note with the proceeding code:

1. We are importing Circulate UI to the component, and then using those components in the HTML markup. You can find out more about Circulate UI [here](/ui.html)
2. We need to point to the full admin path when create XHR method (this.\$http), and then pass in the data.
3. The data needs to be reset back to default when you get a successful response. Admin does not take care of this for you.

> NOTE: We plan on adding Mixin methods available to handle some of these things for oyu in the future.
> NOTE: You'll most likely want to have a basic understanding to Vue to work with the Admin interface efficiently.

Now, you'll want to create a post. If you go to the post page, you should see the two fields, title (a field) and body (a rich text editor). Input the following data, and hit the "add" button:

title: Test
body: This ia a test body.

Once they are added, you can go to a couple of the other routes that we had setup outside of the application. Go to the following URLs (assuming you have kept the defaults when starting the application): 

http://localhost:3000/api/posts
http://localhost:3000/api/posts/1

You should see the the following JSON data returned:

```json
{
  "posts": [
    {
      "id": 1,
      "title": "Test",
      "body": "{\"ops\":[{\"insert\":\"This is a test body.\\n\"}]}"
    }
  ]
}
```

```json
{
  "post": {
    "title": "Test",
    "body": "{\"ops\":[{\"insert\":\"This is a test body.\\n\"}]}"
  }
}
```

If you put this out on the internet, this is the data you would see returned. You can then add a front end that consumes this data. Something like [Nuxt](https://nuxtjs.org/), or if you prefer, leave it as an API. It all depends on your use case.

This was a very simple example of the start of a blogging application. Not only showing that you can create your own interface within the provided Admin bundled with the framework, but you can create your database, and return exactly the data you want or need when you need it, and quickly. We believe this will empower developers to create very powerful API's or headless CMS's very quickly, while creating a structure that is easy to maintain.

We look forward to seeing what you create!
