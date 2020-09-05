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
yarn start

# Or 

npm start
```

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

By default, CirculateJS doesn't do much, other than provide an administration interface (/admin by default). The power of the application comes from plugins.  The idea behind this is to allow you to create the exact application that you want, without the need for anything extra, or hacking your way through it. This also has the added benefit of keeping your different application logic separated.

So now, how do we go about adding a plugin? You just need to add a directory (preferably as the name of your plugin), and make sure there is a manifest file available for the application to read.

For more detailed information, visit the [plugin page](plugins.html)
