# body-parser

Link: [body-parser](https://www.npmjs.com/package/body-parser)

Node.js body parsing middleware.
Parse incoming request bodies.
It's available under `req.body` property

## Installation

```
npm i body-parser
```

## Usage

```
const express = require('express')
const bodyParser = require('body-parser')

const app = express()

// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }))

// parse application/json
app.use(bodyParser.json())

app.use(function (req, res) {
  res.setHeader('Content-Type', 'text/plain')
  res.write('you posted:\n')
  res.end(JSON.stringify(req.body, null, 2))
})
```
