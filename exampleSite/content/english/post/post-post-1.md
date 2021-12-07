---
title: "Configure and deploy React projects with Webpack, Babel, and Amazon EC2"
date: 2021-09-29T11:07:10+06:00
author: Gi Tayier
image : "images/blog/blog-post-1.jpg"
bg_image: "images/feature-bg.jpg"
categories: ["Tech"]
tags: ["Tutorial","Technology"]
description: ""
draft: false
type: "post"
---

> In this tutorial, you will first learn how to set up your React project using Webpack and Babel...


React is one of the most popular web application frameworks, and React developers often deploy to Amazon EC2 to benefit from its scale and reach their customers across the world. React developers also use asset packaging tools such as Webpack and Babel to bundle their assets and modules for easier deployment. By hosting on AWS, React developers like you can serve customers everywhere and quickly scale to accommodate unexpectedly high customer demand. <br /> <br />In this tutorial, you will first learn how to set up your React project using Webpack and Babel. Then, you will learn how to deploy your project to an EC2 Linux instance.

## Contents

 * Configuring React with Webpack and Babel using the AWS Command Line Interface (CLI)
 Initializing a React project with npm and Git
 * Setting up entry and output paths for Webpack
 * Installing Webpack and Babel
 * Configuring Webpack
 * Running Webpack
 Deploying to an EC2 Linux instance
 * Logging in to AWS to view the AWS Management Console
 * Launching an EC2 instance
 * Connecting from Mac or Linux to your EC2 instance
 * Configuring EC2 instance



## Prerequisites

Before you begin, ensure that you have installed Node.js, npm, and the AWS CLI on your local machine and that you have created an AWS account.


##### *[Install npm](https://docs.npmjs.com/getting-started)* |  *[Install Node.js](https://nodejs.org/en/download/)* | *[Install AWS CLI](https://aws.amazon.com/cli/)* | *[Create an AWS Account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)*




## Section 1: Configure React with Webpack and Babel using the AWS CLI

### Step 1: Initializing a React project with npm and Git

#### 1.1  —  In the AWS CLI, create and navigate to your new directory with the following commands:


>```
> mkdir YOUR_PROJECT_DIRECTORY_NAME
> cd YOUR_PROJECT_DIRECTORY_NAME
>```


#### 1.2  —  Create your local Git repository and connect it to your Github remote repository.

Initialize a Git repository.
>```
>git init .
>```
Create a new repository on GitHub and add it as a remote to your Git repository.
>```
>git remote add origin YOUR_APPICATION_GITHUB_URL
>```
#### 1.3  —  Initialize your npm project and install React.
 >```
 >npm init -y
 >npm install react react-dom
 >```



### Step 2: Setting up entry and output paths for Webpack
#### 2.1 — Create a source (“src”) directory.

The src directory is the entry point for Webpack to bundle your application’s files and assets. Webpack collects those assets in your source directory.
>``` mkdir src ```

#### 2.2 — Setup React.
Create a new index.js file in the src folder.
>```
>cd ../src
>touch index.js
>```
Using a code editor of your choice, open your newly-created index.js and create a React component:
>```
>import React from "react";
>import ReactDOM from "react-dom";
>class ReactProject extends React.Component {
>  render() {
>    return <h1>React Project</h1>;
>  }
>}
>ReactDOM.render(<ReactProject />, document.getElementById("root"));
>```

#### 2.3 — Create an output path for Webpack.
Create the distribution (“dist”) directory to specify where Webpack creates and stores the bundle of all the JavaScript necessary for your application to run.

>```
> cd dist
> touch index.html
>```


To use the bundle in a browser script, add a \<script\> tag within the index.html file body:


>```
><!DOCTYPE html>
>  <html>
>    <head>
>      <title>react-project</title>
>    </head>
>    <body>
>      <div id="root"></div>
>      <script src="./bundle.js"></script>
>    </body>
>  </html>
>```


### Step 3: Installing Webpack and Babel
#### 3.1 — Install Webpack modules.
 >```npm install webpack```
 Install webpack-dev-server to test your project in your local browser. Also, install webpack-cli access Webpack commands used later in this tutorial.
>``` npm install webpack-dev-server webpack-cli```

#### 3.2 — Install Babel Modules

Install babel core which transpiles JavaScript files using Babel and Webpack. Transpiling JavaScript allows a larger number of browsers to use your application and is a common web developer practice.

Install babel-loader.

>```
>npm install @babel/core
>npm install babel-loader
>```

To use the babel-loader, you must install:
- @babel/preset-env, which is the default Babel preset used to transform ES6+ into valid ES5 code,
- @babel/preset-react, which transforms React class syntax into JavaScript code, and
- @babel/plugin-transform-runtime, which enables the re-use of Babel's injected helper code to avoid duplication across your compiled bundle.

>```
> npm install @babel/preset-env \@babel/preset-react
> @babel/plugin-transform-runtime
>```

### Step 4: Configuring Webpack
By default, Webpack searches for a JavaScript file with the name webpack.config.js in your root folder. This file specifies your configuration options.

Create webpack.config.js in your root directory.
>```touch webpack.config.js```


To get started, add the following to your webpack.config.js file:
>```js
>// Import path for resolving file paths
>var path = require('path');
>module.exports = {
> // Define entry point for webpack
>  entry:  __dirname + '/src/index.js',
> // Define output path for the bundled file
>  output: {
>    path: __dirname + '/dist',
>    publicPath: '/',
>    filename: 'bundle.js'
>  },
>  mode: 'development',
>  devServer: {
>    contentBase: './dist',
>  },
>  module: {
>    rules: [
>       {
>        test: /\.(js|jsx)?/,
>        exclude: /node_modules/,
>        use: {
>          loader: 'babel-loader',
>          options: {
>            presets: ['@babel/preset-env', '@babel/preset-react'],
>            plugins: [
>                       [
>                         '@babel/plugin-transform-runtime',
>                         { regenerator: true }
>                       ]
>                     ],
>          },
>        },
>      },
>    ]
>  },
>};
>```

### Step 5: Running Webpack
#### 5.1 — To use Webpack, add the following to the scripts object in your package.json file.
>```
>"build": "npx webpack --config ./webpack.config.js"
>```
Here is an example package.json with the Webpack command. Consider this example of the code used to add a Webpack command.
>```
>{
>  "name": "react-project",
>  "description": "",
>  "version": "1.0.0",
>  "main": "index.js",
>  "scripts": {
>    "build": "npx webpack --config ./webpack.config.js --mode development -w",
>   },
>  "devDependencies": {
>    "@babel/core": "^7.15.0",
>    "@babel/plugin-transform-runtime": "^7.15.0",
>    "@babel/preset-env": "^7.15.0",
>    "@babel/preset-react": "^7.14.5",
>    "babel-loader": "^8.2.2",
>    "webpack": "^5.51.1",
>    "webpack-cli": "^4.8.0"
>  },
>}
>```


#### 5.2  —  Run the script by typing the following command.
>```npm run build```

#### 5.3  — To keep your Git history clean, create a .gitignore file.
>``` touch .gitignore```

Add the following content to your .gitignore file.
>```
> node_modules/
> bundle.js
>```

#### 5.4  — Add, commit, and push your local repository to your GitHub repository.
 >```
 >git add .
 >git commit -m "YOUR_COMMIT_MESSAGE"
 >git push
>```

## Section 2: Deploy to EC2 Linux instance
Now that your React project is bundled and pushed to your GitHub remote repository, you can deploy your application to an EC2 Linux instance.
### Step 1: Logging in to AWS to view the AWS Management Console
#### 1.1  — Sign in to an AWS account as an IAM user.
<!-- ![screenshot1.1](/images/feature-bg.jpeg) -->
#### 1.2  — Navigate to the AWS Management Console.
<!-- ![screenshot1.2](images/image3.png) -->
### Step 2:  Launch an EC2 instance
#### 2.1 — Launch a virtual machine with EC2.
From the console, under Build a solution, choose Launch a virtual machine.
<!-- ![screenshot2.1](/images/image2.png) -->
#### 2.2  —  Choose an Amazon Machine Image (AMI).
Choose the HVM edition of the **Ubuntu Server LTS 20.04**. This AMI has the advantage of being Free tier eligible and requires fewer steps to install packages.
<!-- ![screenshot2.2](/images/image5.png) -->
#### 2.3  —  Choose an Instance Type.
To remain free tier eligible, choose the t2.micro as the hardware configuration of your instance. Then choose Review and Launch.
<!-- ![screenshot2.3](/images/image4.png) -->
#### 2.4 — Launch your instance.
Ensure your configuration and settings are correct, then choose Launch.
<!-- ![screenshot2.4](/images/image6.png) -->
#### 2.5 — Configuring a key pair.
Choose Create a new key pair and enter a name. Then choose Download Key Pair and click Launch Instance.

Note: You will connect to the instance with the private key file in the next step, so be sure to save it and keep it safe.
<!-- ![screenshot2.5](/images/image9.png) -->

### Step 3: Connect from Mac or Linux to your Amazon EC2 Instance

#### 3.1 — On EC2 Dashboard page, under Resources, choose Instances.
<!-- ![screenshot3.1](/images/image8.png) -->

#### 3.2 — Select the instance you just created and click Connect.
<!-- ![screenshot3.2](/images/image11.png) -->
#### 3.3 — Connect to your instance.

Navigate to the directory of the private key file you downloaded. Use the following two commands highlighted on the Connect to instance page to set the permission of your private key file and connect to your instance. For more information about how to set the permission of your private key file, see [Connect to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html).
<!-- ![screenshot3.3](/image10.png) -->
### Step 4: Configuring your EC2 instance

#### 4.1 — Update instance software on your ES2 instance.

To update all packages on your EC2 instance, type the following commands
>```
>sudo apt-get update
>sudo apt-get upgrade -y
>```
#### 4.2 — Install Node and Git

To install the latest version of Node.js, type the following commands:
 >```
 >curl -sL https://deb.nodesource.com/setup/setup_14.x | sudo -E bash -
 >sudo apt-get install -y nodejs
 >Sudo apt-get install git
>```
#### 4.3 — To clone your project to your EC2 instance, run the following command:
>```git clone YOUR_PROJECT_GITHUB_URL ```

#### 4.4 — Install and build your application on your EC2 instance.

Navigate to your repository, install dependencies, and run a build script to build your bundled file.
>```
>cd YOUR_PROJECT_DIRECTORY_PATH
> npm install
> npm build
>```

## Congratulations

#### You learned how to configure and deploy your React project using Webpack, Babel, and Amazon EC2 to easily deploy, manage, and scale your application.


</p>
