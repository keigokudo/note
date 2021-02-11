# nodemon

nodemon is a tool that helps develop node.js based applications by automatically restarting the node application when file changes in the directory are detected.

Link: [nodemon](https://www.npmjs.com/package/nodemon)

## Installation

```
npm install --save-dev nodemon
```

## How to use

You can start nodemon with this command

```
nodemon server.js
```

But fixing a package.json is probably better.

Example:

```
 "scripts": {
    "start": "nodemon server.js"
  }
```
