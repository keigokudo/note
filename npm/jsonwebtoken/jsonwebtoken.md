# jsonwebtoken

Link: [npm jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken)

Link: [JWT](https://jwt.io/)

## Instllation

```
npm i jsonwebtoken
```

## Usage

This returns JsonWebToken as string.

```
const token = jwt.sign(payload, secretOrPrivateKey, [options])
```

\*You supply can supply backback and it runs asynchronously.

```
// this just decodes the token
jwt.decode()

// this decodes the token and also veryfies it
jwt.veryfy()
```

## Example

```
const token = jwt.sign(
          { email: user.email, userId: user._id },
          // this shoud be saved in .env file
          'some secret key',
          //
          {
            expiresIn: '1h',
          }
        )
```

Token

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InRlc3Q0QHRlc3QuY29tIiwidXNlcklkIjoiNjAzNTBjNTY1OTA0ODU0NTNhYTE4YWFhIiwiaWF0IjoxNjE0MTcxODA0LCJleHAiOjE2MTQxNzU0MDR9.JQe8PPbIzFMf24WthXHB_qnYpeLerPtTp-GFFo7HOxI
```

The token is encoded but not decrypted.
You can decode the token at [JWT](https://jwt.io/).
