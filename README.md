# PERN
A tutorial written for our Operating Systems class
## How to create a PostgreSQL, Express, React, Node.js Stack 
1. Install required packages 
```sudo apt install nodejs npm nginx```

2. Install Express
```npm install express```

3. Create Express server (write the following in server.js)

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

This code will start a Node webserver listening on port 3000. It will respond with the contents of the "public" folder, defaulting to returning the index.html file.

4. Create public folder & insert index.html

```mkdir public
vim public/index.html
```

5. Install & Configure PM2 to run Node as a service
```
sudo npm install pm2@latest -g
pm2 start server.js 
pm2 startup systemd
sudo env PATH=$PATH:/usr/bin /usr/local/lib/node_modules/pm2/bin/pm2 startup systemd -u cit352 --hp /home/cit352
pm2 save
systemctl enable pm2-cit352
sudo systemctl start pm2-cit352
```

6. Set up Nginx to reverse proxy port 80 to port 3000
This helps with security so that we don't have to run Nodejs as root.

Edit the nginx config file

```sudo vim /etc/nginx/sites-available/default
```
```
server {
...
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
...
}
```

```sudo nginx -t
sudo systemctl restart nginx
```

![alt text](https://github.com/sang-chu/pern-stack/raw/main/images/amogus.png "Our fully functioning NodeJS & Express server")

Now we have 2 parts of the stack built, NodeJS & Express, and have incorporated Nginx to safely handle our web traffic.
