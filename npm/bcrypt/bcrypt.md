# bcrypt

bcrypt encrypts password.

Link: [brypt](https://www.npmjs.com/package/bcrypt)

## Usage

### How to hash a password

```
bcrypt.hash(plainTextPassword, salt, function(err, hash) {
    // Store hash in your password DB.
});
```

**What is salt?**
_Salt is basically a string which is added to the password before the password is hashed. This makes the hash more secure.
You also can add a number and bcrypt generates the string automatically._

### How to check a password

```
// Load hash from your password DB.
bcrypt.compare(myPlaintextPassword, hash, function(err, result) {
    // result == true
});
```

## Example

```
const express = require('express')
const router = express.Router()
const mongoose = require('mongoose')
const bcrypt = require('bcrypt')

const User = require('../models/user')

router.post('/signup', (req, res, next) => {
  bcrypt.hash(req.body.password, 10, (err, hash) => {
    if (err) {
      return res.status(500).json({ error: err })
    } else {
      const user = new User({
        _id: new mongoose.Types.ObjectId(),
        email: req.body.email,
        password: hash,
      })
      user
        .save()
        .then((result) => {
          res.status(200).json({
            message: 'user created',
          })
        })
        .catch((err) => {
          console.log(err)
          res.status(500).json({
            message: err,
          })
        })
    }
  })
})

module.exports = router

```

Or _bcrypt.hashSync(plainPassword, saltRounds)_ can be cleaner.

```
  const saltRounds = 10
  const hash = bcrypt.hashSync(req.body.password, saltRounds)
  const user = new User({
    _id: new mongoose.Types.ObjectId(),
    email: req.body.email,
    password: hash,
  })
```
