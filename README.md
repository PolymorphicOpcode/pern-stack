# PERN
## How to create a PostgreSQL, Express, React, Node.js Stack 
1. Install Nginx, NodeJS & NPM Package Manager
sudo apt install nodejs npm nginx

2. Install Express
npm install express

3. Create Express server

```const express = require('express')
const app = express()
const port = 3000

app.use(express.static('public'))

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`)
})
```

4. Create public folder & insert index.html
