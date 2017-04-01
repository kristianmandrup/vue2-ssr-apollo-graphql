# vue2-ssr-example

[![Greenkeeper badge](https://badges.greenkeeper.io/kristianmandrup/vue2-ssr-apollo-graphql.svg)](https://greenkeeper.io/)

Vue 2.0 Server-Side Rendering Example. [Read more](http://csbun.github.io/blog/2016/08/vue-2-0-server-side-rendering/).

## Installation

```bash
npm install
```

## Development

```bash
npm start
```

Hit [http://127.0.0.1:3000/](http://127.0.0.1:3000/) on your favorite browser.

### Architecture

The Server app uses an Express web server.

`npm start` runs `server/index.js`

It starts by creating the server and client bundle files:

```js
// Server-Side Bundle File
const serverBundleFilePath = path.join(__dirname, '../dist/bundle.server.js')
const serverBundleFileCode = fs.readFileSync(serverBundleFilePath, 'utf8');
const bundleRenderer = vueServerRenderer.createBundleRenderer(serverBundleFileCode);

// Client-Side Bundle File
const clientBundleFilePath = path.join(__dirname, '../dist/bundle.client.js');
const clientBundleFileUrl = '/bundle.client.js';
```

It then sets up the default route to make the (server) `bundleRenderer` write the `html` of the response and the client bundle file url in the script tag.

```html
<body>
  ${html}
  <script src="${clientBundleFileUrl}"></script>
</body>
```

The full SSR:

```js
// Server-Side Rendering
app.get('/', function (req, res) {
  bundleRenderer.renderToString((err, html) => {
    if (err){
      res.status(500).send(`
        <h1>Error: ${err.message}</h1>
        <pre>${err.stack}</pre>
      `);
    } else {
      res.send(`
        <!DOCTYPE html>
        <html>
          <head>
            <meta charset="utf-8">
            <title>Vue 2.0 SSR</title>
          </head>
          <body>
            ${html}
            <script src="${clientBundleFileUrl}"></script>
          </body>
        </html>`);
    }
  });
});
```

Finally it adds a route for the client file URL to respond with the contents of the bundled client (Vue2 app) file.

```js
app.get(clientBundleFileUrl, function (req, res) {
  const clientBundleFileCode = fs.readFileSync(clientBundleFilePath, 'utf8');
  res.send(clientBundleFileCode);
});
```

### More notes

Notice that the `ssr-bundle.js` and `ssr.js` files in the root are just sandbox files, used for testing the functionaliy.

### Useful tools

- [vue-webpack](https://github.com/xpepermint/vue-webpack) Webpack configuration object generator for Vue.js.
- [vue-builder](https://github.com/xpepermint/vue-builder) Server-side and client-side rendering for Vue.js.
- [express-vue-builder](https://github.com/xpepermint/express-vue-builder)Vue.js server-side and client-side rendering middleware for Express.js.

## WIP integrating GraphQL via Apollo

WIP to add GraphQL integration via Apollo.

Hello world Apollo Vue2 app:

- [frontpage client](https://github.com/Akryum/frontpage-vue-app)
- [frontpage server](https://github.com/apollostack/frontpage-server)

### Add main.js




