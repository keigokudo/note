# mongoose

## Installation

```
npm i mongoose
```

## Usage

1. Connect to the database

   ```
   const mongoose = require('mongoose')
   const uri = mongodb+srv://<username>:<password>@cluster0.ei5mh.mongodb.net/<dbname>?retryWrites=true&w=majority

   mongoose.connect(uri, { useUnifiedTopology: true, useNewUrlParser: true })
   ```

2. Crate a Schema

```
const sampleSchema = mongoose.Schema({
  _id: mongoose.Schema.Types.ObjectId,
  name: String,
  number: Number,
})
```

3. Compile the schema into a Model

```
mongoose.model('Sample', sampleSchema)
```

4. CRUD is explained below

### POST

### GET

### PATCH

### DELETE
