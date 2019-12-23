# WebSocket Proxy

This is a simple Node.js WebSocket server that, upon connection, creates its own connection to the WebSocket server specified by the `target` query parameter, and relays messages between both.

> **Notice:** `websocket-proxy` was formerly known as `mpp-proxy-server` and was hard-coded to just connect to Multiplayer Piano's server. See the example URI below for using `websocket-proxy` with Multiplayer Piano.


## Advantages over “forward” proxies (HTTPS, SOCKS):
- It can be hosted on Node.js web application platforms that support WebSockets, such as Heroku, OpenShift, Repl.it, and Glitch.
    - This allows you to have a fast, reliable & private proxy, unlike if you were using a public proxy from a list.
    - Most web app platforms let you connect with TLS, so you can have TLS encryption on your end with WebSocket servers that do not support it.
- It works with web browsers!
    - You can use it to circumvent cross-origin restrictions.

## Installation

### On [Heroku](https://www.heroku.com/)
1. Fork this repository.
2. Create a new app in Heroku with the name of your choice.
3. Under `Deploy`, choose to Deploy from GitHub and select your forked repository.
4. Click `Open App` to get the app URL.

### On [RedHat OpenShift](https://www.openshift.com/)
1. Create your starter app and wait for it to be provisioned.
2. Choose Node.js in the Catalog.
3. Name the project whatever you want; set the Application name to `websocket-proxy` and enter this repository's URL.
4. Take note of the app URL on the upper-right of the Overview page.

### On other cloud IDE platforms
- On [Repl.it](https://repl.it), you can fork this “repl”: https://repl.it/@ledlamp89/websocket-proxy
- For [Glitch](https://glitch.com), click this: https://glitch.com/edit/#!/remix/clone-from-repo?REPO_URL=https://github.com/ledlamp/websocket-proxy
- On other platforms, if you can't clone the repository, just manually upload the files or copy and paste the index.js file. You might need to modify something.

### On a Linux system
```sh
git clone https://github.com/ledlamp/websocket-proxy.git
cd websocket-proxy
npm install
# the server defaults to port 8080, but you can set the PORT environment variable
export PORT=80     # for example
# for temporary run just do
npm start
# to run as daemon (in background)
nohup npm start &
# or you can use your preferred process manager (pm2, forever, etc)
# and don't forget to make it start at reboot (such as in `crontab -e` or with `pm2 startup`)
# if you want it to be permanently available.
```
> If you need to install node.js on shared hosting, see [this example](https://gist.github.com/ledlamp/6602505c520e7434d06239a72204091d)


## Usage

Unlike HTTPS or SOCKS proxies, no proxy agent is needed. Just connect to the WebSocket proxy server directly.

The connection URI will be in this format:
```
ws(s)://<hostname/ip>(:<port>)/?target=<targetURI>(&<headername>=<headervalue>…)
```

> If using a cloud application platform, copy the app's URL and replace `http://` with `ws://` or `https://` with `wss://`. Then append the target parameter with the target URI.

The `target` parameter is required to specify where the proxy should connect to. All other parameters are set as headers.

For example, to connect to Multiplayer Piano's server (which requires an origin header) via a WebSocket proxy on localhost port 8080:
```
ws://localhost:8080/?target=ws://www.multiplayerpiano.com:443&origin=http://www.multiplayerpiano.com
```
Example: To use with MPP in browser, paste into console:
```js
MPP.client.stop();
MPP.client.uri = "ws://localhost:8080/?target=ws://www.multiplayerpiano.com:443&origin=http://www.multiplayerpiano.com";
MPP.client.start();
```
Query parameters may or may not be encoded, but querystring chars (`&` and `=`) must be encoded to escape them.

**Note:** If the `target` is missing or invalid, or if an error occurs when connecting to the remote host (such as if it responded with a 403), your connection is simply closed. Ideally, the proxy server would wait for the connection to the target to finish, before responding to the client with the same response of the target; however, I found this much too complicated to set up, so I just kept it simple.
