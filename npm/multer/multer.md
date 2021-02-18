# multer

Multer is a node.js middleware for handling multipart/form-data, which is primarily used for uploading files.

multer is like an alternative for body-parser. multer parses multipart/form-data.

Link: [multer](https://www.npmjs.com/package/multer)

## Installtion

```
npm i multer
```

## Usage

### Basic usage

```
// import multer
const multer = require('multer')
// set the directory
const upload = multer({ dest: 'uploads/' })

// parse before you post
router.post('/', upload.single('sampleImage'), (req, res, next) => {
  ...
  // omit below
}

```

Don't forget to change the post body's content type to form-data when you test this.

### Usage with storage

Although you can upload files with Basic usage above but you can get a full contorol with DiskStorage.

```
// import multer
const multer = require('multer')
//
const storage = require(multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, './uploads/')
  },
  filename: (req, file, cb) => {
      // name the uploaded file
    cb(null, new Date().toISOString() + file.originalname)
  },
}))
// pass stroge to multer
const upload = multer({ storage: storage })

// parse before you post
router.post('/', upload.single('sampleImage'), (req, res, next) => {
  ...
  // omit below
}

```

## Filter

```
// config how you restrict the incoming file
const fileFilter = (req, file, cb) => {
  if (file.mimetype === 'image/jpeg' || file.mimetype === 'image/png') {
    // accept the file
    cb(null, true)
  } else {
    // reject the file
    cb(new Error({message: 'file type must be jpeg or png'}), false)
  }
}

// pass it multer
const upload = multer({
  storage: storage,
  limits: { fileSize: 1024 * 1024 * 5, fileFilter: fileFilter },
})
```

## Example of inside the req.file

```
{
  fieldname: 'sampleImage',
  originalname: 'unicorn.png',
  encoding: '7bit',
  mimetype: 'image/png',
  destination: './uploads/',
  filename: 'unicorn.png-2021-02-17T15:44:53.934Z',
  path: 'uploads/unicorn.png-2021-02-17T15:44:53.934Z',
  size: 168372
}
```

##
