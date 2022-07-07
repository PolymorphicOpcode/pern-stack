# PERN
A tutorial written for our Operating Systems class
## How to create a PostgreSQL, Express, React, Node.js Stack 

We registered an ubuntu server on DigitalOcean, ssh'ed in, and created a user "cit352".

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

Write anything in the index.html file, you will change this later to implement our React.

5. Install & Configure PM2 to run Node as a service
```
sudo npm install pm2@latest -g
pm2 start server.js 
pm2 startup systemd
sudo env PATH=$PATH:/usr/bin /usr/local/lib/node_modules/pm2/bin/pm2 startup systemd -u `whoami` --hp /home/`whoami`
pm2 save
systemctl enable pm2-cit352
sudo systemctl start pm2-cit352
```

6. Set up Nginx to reverse proxy port 80 to port 3000
This helps with security so that we don't have to run Nodejs as root.

Edit the nginx config file

```
sudo vim /etc/nginx/sites-available/default
```

Then input the following information within the "location /" section fo the configuration

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

Then set the nginx configuration and restart the nginx server

```sudo nginx -t
sudo systemctl restart nginx
```

![NodeJS & Express Server](https://github.com/sang-chu/pern-stack/raw/main/images/amogus.png "Our fully functioning NodeJS & Express server")

Now we have 2 parts of the stack built, NodeJS & Express, and have incorporated Nginx to safely handle our web traffic.

7. Install PostgreSQL

```
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql.service
sudo systemctl enable postgresql.service
```

We first start the service, then enable it so that it is started upon boot.

```
sudo -u postgres createuser --interactive
```

```
Enter name of role to add: cit352
Shall the new role be a superuser? (y/n) y
```

We then create a role called 'cit352', which will administer our database.

```
sudo -u postgres createdb cit352
sudo adduser cit352
psql -d cit352
\conninfo
\q
```

Since our user was already called "cit352", the adduser command was not necessary. The "cit352" user was already a member of the "cit352" group that was created for postgresql access, so we were able to skip the adduser step.

https://codepen.io/kylewetton/pen/gjjGdX

I then copied the code from this pen to our index.html within the public folder, and converted the Javascript & SCSS respectively to a vanilla format that our server can understand.

https://babeljs.io/repl
https://www.cssportal.com/scss-to-css/

I added the converted code into <style> html tags in the head, and the Javascript in an inline <script> at the bottom of the body.

Then, add the following external Javascript references to import React.

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/react/16.4.2/umd/react.production.min.js"/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react/16.4.2/umd/react.production.min.js"/>
```

Now we have a cool snake game being served on our web server!

![finished photo](https://raw.githubusercontent.com/sang-chu/pern-stack/main/images/finished.png "Our finished PERN server")

This implements React.JS, the last part of our PERN stack server.
![finished gif](https://raw.githubusercontent.com/sang-chu/pern-stack/main/images/restarting.gif)
