---
layout: post
title: Install Homepage in a Container
categories: [homelab, lxcs]
tags: [container, linux, homepage, proxmox]
after-content: [disclaimer-notice.html]
---
Create an Alpine Linux proxmox container.
~~~
apt update
apk add nano npm git
git clone https://github.com/benphelps/homepage.git
mkdir /root/homepage/config
cd /root/homepage
npm run build
npm run start
~~~

If you want to change the port homepage uses from 3000 to 80, do the following.

[https://stackoverflow.com/a/69780065](https://stackoverflow.com/a/69780065) references the comment [https://github.com/vercel/next.js/pull/11408#issuecomment-784867637](https://github.com/vercel/next.js/pull/11408#issuecomment-784867637) describing one way we can change the port that a node.js application binds to.

This method requires dotenv so we need to install it before we continue with these instructions.

```npm install dotenv```

>Create a script for your prod environment in the project root e.g. prod-server.js
>~~~
>// prod-server.js
>require('dotenv').config(); // require dotenv
>const cli = require('next/dist/cli/next-start');
>
>cli.nextStart(['-p', process.env.PORT || 3000]);
>~~~
>
>
>Update the start command in your package.json to use the prod-server.js script like this:
>
>~~~
>  "scripts": {
>    "build": "next build",
>    "start": "node prod-server.js"
>  }
>
>~~~

For our use case, we are going to change "3000" to "80" because we want Homepage to bind to port 80. Here is what our updated prod-server.js file looks like:

>~~~
>// prod-server.js
>require('dotenv').config(); // require dotenv
>const cli = require('next/dist/cli/next-start');
>
>cli.nextStart(['-p', process.env.PORT || 80]);
>~~~

Here is what our updated package.json file looks like:

>~~~
>  "scripts": {
>    "build": "next build",
>    "start": "node prod-server.js"
>  }
>
>~~~

Then we need to build the node.js application and run it.<sup>1</sup>

```npm run build```

```npm run start```

[1] [https://stackoverflow.com/a/69780065](https://stackoverflow.com/a/69780065)