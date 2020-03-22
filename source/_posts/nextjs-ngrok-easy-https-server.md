---
title: Easy https server with nextjs and ngrok
date: 2020-03-22 00:20:00
---

So I had this app where you must share a link to other person and I remembered about the [web share API](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/share). After a few lines of code I was able to have the feature almost ready, but then I realize this feature only works under an HTTPS connection.

You probably already know nextjs is so great, but in order to have an https server on localhost you need to create a [custom server](https://nextjs.org/docs/advanced-features/custom-server), generate certificates with openssl and I was not in the mood to do that work. 

I was looking for a solution that save me the task to write a custom server so I decide to combine nextjs with ngrok and it was very easy, keep reading to found how it works:

First, you will need to install the dependencies for next

```bash
npm install next react react-dom
```

Now let's install [ngrok](https://www.npmjs.com/package/ngrok) as a dev depedency

```bash
npm install ngrok -D
```

ok so let's create the scripts in our **package.json**

```json
{
    "scripts": {
        "dev": "next",
        "ngrok": "ngrok http 3000"
    }
}
```

The **dev** script is just executing the **next** command and will launch the server without https support. And the **ngrok** script is going to create a tunnel between your local server and the internet. In order to do that ngrok needs the port where your local server is running, in our case the default port for next is 3000. That's why the script is **ngrok http 3000**

Ok now let's create the **pages** directory and an index file inside of it.

```js
// /pages/index.js

function Home() {
  async function onShare() {
    await navigator.share({
      title: "Best React Framework",
      url: "https://nextjs.com"
    });
  }

  return (
    <div>
      <button onClick={onShare}>Share</button>
    </div>
  );
}

export default Home;
```

Nothing fancy but works for our example, a simple page with a button and a click event associated to it. If the button is pressed it will call the web share api and open the native share dialog of the operating system.

### Running the servers

Ok let's run the next server first

```bash
npm run dev
```

Once the server is ready you will see in your terminal a message like this:

```bash
[ ready ] compiled successfully - ready on http://localhost:3000
```

Now is the turn for ngrok, let's run the script

```bash
npm run ngrok
```

Once ngrok is ready it will show you a log like this:

```
Session Expires               7 hours, 35 minutes
Version                       2.3.35
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://70d96b45.ngrok.io -> http://localhost:3000
Forwarding                    https://70d96b45.ngrok.io -> http://localhost:3000
```

Following the above logs you have two urls now:

```
http://70d96b45.ngrok.io # http server
https://70d96b45.ngrok.io # https server 
```

Ok since the web share API right now only works on Safari for IOS and Chrome for android I will open the url in my iphone and tap the button.

![NextJs with ngrok](https://eperedo-blog.s3.amazonaws.com/nextjs/share_web_api_ios.png)

And that's it you can test this kind of features without a custom server, boring steps with openssl. If you want more info about ngrok you can check the [website](https://ngrok.com/).

Hope it helps!
