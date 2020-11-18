# svelte app

This is a project template for [Svelte](https://svelte.dev) apps. It lives at https://github.com/jeanmarcboite/sveltejs-template-webpack.

It is a modified version of https://github.com/sveltejs/template-webpack

To create a new project based on this template using [degit](https://github.com/Rich-Harris/degit):

```bash
npx degit jeanmarcboite/sveltejs-template-webpack svelte-app
cd svelte-app
```

## Install babel

```bash
npm i --save-dev @babel/core @babel/plugin-proposal-class-properties @babel/preset-env
npm i --save-dev babel-loader
npm i --save-dev svelte-preprocess
```

## Install tailwind

```bash
npm i --save-dev autoprefixer postcss postcss-cli postcss-import@12 postcss-nested
npm i --save-dev tailwindcss
```

- create a file src/Tailwind.svelte

```javascript
<style global lang="postcss">
  /* only apply purgecss on utilities, per Tailwind docs */ /* purgecss start
  ignore */ @tailwind base; @tailwind components; /* purgecss end ignore */
  @tailwind utilities;
</style>
```

- modify App.svelte

```javascript
<script>
  import Tailwind from "./Tailwind.svelte";

  export let name;
</script>

<style lang="postcss">
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;

      .tw {
        @apply font-sans text-lg text-center text-green-800;
      }
    }
  }
</style>

<Tailwind />
<main>
  <h1>Hello {name}!</h1>
  <p class="tw">tailwind included<span class="text-red-800">!</span></p>
  <p>
    Visit the
    <a href="https://svelte.dev/tutorial">Svelte tutorial</a>
    to learn how to build Svelte apps.
  </p>
</main>
```

- add rules to webpack.config.js

```javascript
{
        test: /\.m?js$/,
        exclude: /(node_modules|bower_components)/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
            plugins: ['@babel/plugin-proposal-class-properties'],
          },
        },
      },

      {
        test: /\.svelte$/,
        use: {
          loader: 'svelte-loader',
          options: {
            emitCss: true,
            hotReload: true,
            preprocess: require('svelte-preprocess')({
              // https://github.com/kaisermann/svelte-preprocess/#user-content-options
              sourceMap: !prod,
              postcss: {
                plugins: [
                  require('tailwindcss'),
                  // require("autoprefixer"),
                  require('postcss-nested'),
                ],
              },
            }),
          },
        },
      },
```

## Install storybook

- run cli

```bash
npx -p @storybook/cli sb init --type svelte  --use-npm
```

- add postcss to .storybook/main.js, via webpackFinal

```javascript
module.exports = {
  stories: ['../src/**/*.stories.mdx', '../src/**/*.stories.@(js|jsx|ts|tsx)'],
  addons: ['@storybook/addon-links', '@storybook/addon-essentials'],
  webpackFinal: async (config) => {
    const svelteLoader = config.module.rules.find(
      (r) => r.loader && r.loader.includes('svelte-loader'),
    )
    svelteLoader.options = {
      ...svelteLoader.options,
      preprocess: require('svelte-preprocess')({
        postcss: {
          plugins: [
            require('tailwindcss'),
            require('autoprefixer'),
            require('postcss-nested'),
          ],
        },
      }),
    }

    return config
  },
}
```

- preload tailwindcss in preview-head.html

```javascript
<link
  rel="stylesheet"
  href="https://cdnjs.cloudflare.com/ajax/libs/tailwindcss/1.9.6/tailwind.min.css"
  integrity="sha512-l7qZAq1JcXdHei6h2z8h8sMe3NbMrmowhOl+QkP3UhifPpCW2MC4M0i26Y8wYpbz1xD9t61MLT9L1N773dzlOA=="
  crossorigin="anonymous"
/>
```

## Install electron

```bash
npm i electron
npm i chokidar cross-env npm-run-all --save-dev
```

_Note that you will need to have [Node.js](https://nodejs.org) installed._

## Get started

Install the dependencies...

```bash
cd svelte-app
npm install
```

...then start webpack:

```bash
npm run dev
```

Navigate to [localhost:8080](http://localhost:8080). You should see your app running. Edit a component file in `src`, save it, and the page should reload with your changes.

## Deploying to the web

### With [now](https://zeit.co/now)

Install `now` if you haven't already:

```bash
npm install -g now
```

Then, from within your project folder:

```bash
now
```

As an alternative, use the [Now desktop client](https://zeit.co/download) and simply drag the unzipped project folder to the taskbar icon.

### With [surge](https://surge.sh/)

Install `surge` if you haven't already:

```bash
npm install -g surge
```

Then, from within your project folder:

```bash
npm run build
surge public
```
