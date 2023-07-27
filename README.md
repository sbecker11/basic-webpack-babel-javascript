# basic-webpack-babel-javascript
copied from Hridayesh Sharma's work at
https://www.loginradius.com/blog/engineering/write-a-javascript-library-using-webpack-and-babel/

## Step 1 : Create a directory demo and run following command in it.

### Create the package at project root
``````
  $ npm init -y
``````
The above command will create a package.json in your project root. I am using a --y to initialize it with default options.

### Directory Structure

``````
demo  
  |-- src/  
  |-- package.json  
``````

## Step 2: Adding our source code.
Let's add our source code into src directory:

``````
src
 |--index.js
 |--capital.js
 |--addDOMContent.js
 ``````

Our utility library contains two functions capital, to capitalize a string and addDOMContent, to add content to a web page, each in it's own module.

### capital.js
``````
function capital(string) {
  const capitalizedString =
    string.substring(0, 1).toUpperCase() + string.substring(1)
  return capitalizedString
}

export default capital
addDOMContent.js

function addDOMContent(content) {
  const node = document.createElement("h1")
  node.innerText = content
  document.body.appendChild(node)
}

export default addDOMContent
``````
Inside our index.js, we will import these two functions.

### index.js
``````
import capital from "./capital"
import addDOMContent from "./addDOMContent"

export { capital, addDOMContent }
``````
So far we got the source code ready but we still need to bundle it so that the browsers can understand and oh boy!, we need to support some older browsers too 🙄. Anyway, being responsible developers we are going to do that 😎.

## Step 3 : Let's install some of our project dev dependencies as they are only needed during development.

### Install dev dependencies
``````
 $ npm i --save-dev webpack webpack-cli @babel/core @babel/preset-env babel-loader
 ``````

We need webpack to bundle our code and webpack-cli is a command-line tool that uses webpack to do the same. Also webpack requires babel-loader to transpile our ES6 code to ES5 before bundling (Remember, what I said about being responsible developers 😃).

## Step 4: Now let's get our webpack and babel configuration in place. (We are almost there)

## 4.1. : Create a webpack.config.js at the root of the project.

### webpack.config.js
``````
const path = require("path")

module.exports = {
  entry: path.resolve(__dirname, "src/index.js"),
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "index_bundle.js",
    library: "$",
    libraryTarget: "umd",
  },
  module: {
    rules: [
      {
        test: /\.(js)$/,
        exclude: /node_modules/,
        use: "babel-loader",
      },
    ],
  },
  mode: "development",
}
``````
There are a couple of things we need to understand about webpack configuration. Stay with me for a couple more minutes.

entry: In order for webpack to know where to start with, it needs to know the entry point to our app.

module.rules: Each file in Node is treated like a module. Webpack itself understands only Javascript and JSON modules. Since we want to transpile ES6, we need babel-loader and webpack needs to know the rules on how to process the Javascript using the given loader.

output: After creating the bundle, webpack needs to know what name to give it and where to put it.library and libraryTarget are used to expose our library where library being the name,$ here and libraryTarget is the property to configure, how the library will be exposed. Here we will be using UMD. UMD is a module system capable of working everywhere, be it in the client, on the server or elsewhere.

mode: Webpack bundles code into either development mode (unminified) or in production(minified) mode. I am using a hard coded value here for the demo. You can set it using environment variables too.

## 4.2. : Create a .babelrc file at the root of the project.

### .babelrc
``````
{
  presets: ["@babel/preset-env"]
}
``````

@babel/preset-env let's us use the latest Javascript without any polyfills and syntax transforms.babel-loader uses babel under the hood.

### By Now our Project Structure should look like this:
``````
demo
|-- src
|   |-- index.js
|   |-- capital.js
|   |-- addDOMContent.js
|-- webpack.config.js
|-- .babelrc
|-- package.json
|-- node_modules
``````

## Step 5 : One last step. I know I keep saying that but I promise this is last 😬.

We have added our source files, now let's add an npm script to build final code using webpack and modify the main property inside our package.json to point it to our bundled code.

### package.json
``````
{
  "name": "demo",
  "version": "1.0.0",
  "description": "",
+  "main": "dist/index_bundle.js",
+  "scripts": {
+    "build": "webpack"
+  },
  "keywords": [],
  "author": "Hridayesh Sharma",
  "license": "ISC",
  "dependencies": {},
  "devDependencies": {
    "@babel/core": "^7.10.4",
    "@babel/preset-env": "^7.11.0",
    "babel-loader": "^8.1.0",
    "webpack": "^4.44.1",
    "webpack-cli": "^3.3.12",
  }
}
``````
In package.json the main property is a direction to the entry point of the module that the package.json is describing.

Hurray! We have finally created our utility library using ES6.🥳

### Run the build
``````
$ npm run build 
``````
to generate the bundled code and use it in the next step.

Let's test our library now.

## Step 6 : Create and open index.html

### index.html
``````
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Demo</title>
  </head>
  <body>
    <script src="dist/index_bundle.js"></script>
    <script>
      console.log($)
      alert($.capital("hridayesh"))
      $.addDOMContent("Well It Works Fine!!!")
    </script>
  </body>
</html>
``````
Save it and run it in your browser. You will see the name capitalized.


The complete code is available at LoginRadius Engineering Blog Sample Repo

Thanks for reading the blog. For detailed information and execution example of this blog, please refer to the video below:


