# Installation guide
[Video tutorial](https://modulesunraveled.wistia.com/medias/7cdtb3k40h)
- Install [Brew](https://brew.sh/)
```#bin/bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)" 
```
- Install PHP-7.4
```#bin/bash
brew tap shivammathur/php
brew install shivammathur/php/php@7.4
brew link php@7.4
```
- Install Composer
```#bin/bash
brew install composer 
```
- Install Emulsify globally
```#bin/bash
npm install -g @emulsify/cli 
```
- Enter the project folder and install Drupal
```#bin/bash 
composer install 
```
- Initialize Emulsify Theme (in the root folder)
```#bin/bash
emulsify init "Awesome Theme" 
```
- Install drupal/components and drupal/emulsify_twig (in the root folder)
```#bin/bash
composer require drupal/components drupal/emulsify_twig 
```
- Install system for default standards (in the theme previously created)
```#bin/bash
emulsify system install --repository https://github.com/emulsify-ds/compound.git --checkout 1.1.0 
```
- Add tailwindcss and postcss to package.json and install the packages
```json
{
  "dependencies": {
    ...
    "postcss": "^8.4.5",
    "tailwindcss": "^3.0.12",
    ...
  }
}
```
```#bin/bash
npm install
```
- Create a folder called `src` and add a file called `tailwind.pcss` inside with the following information
```scss
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  input {
    @apply border border-gray-500
  }
}

@layer components {
  .block,
  .node {
    @apply mb-4
  }

  .node__content p,
  .node__content ul,
  .node__content ol {
    @apply mb-4 leading-normal
  }

  [id$="-local-tasks"] ul {
    @apply list-disc list-inside
  }
}
```
- Create in the theme folder a file called `tailwind.config.js` and add following information inside
```javascript
module.exports = {
  content: ["./components/**/*.twig"],
  theme: {
    extend: {},
  },
  variants: {
    extend: {},
  },
  plugins: [],
};
```
- Add below lines to `package.json` below lines
```json
"tailwind-dev": "npx tailwindcss --input src/tailwind.pcss --output dist/tailwind.css --watch",
"tailwind-build": "npx tailwindcss --input src/tailwind.pcss --output dist/tailwind.css --minify"
```
- Change `develop` script to below
```json
"develop": "concurrently --raw NODE_OPTIONS=--openssl-legacy-provider \"npm run webpack\" \"npm run tailwind-dev\" \"npm run storybook --openssl-legacy-provider\"",
```
- Add below line to `main.js`(inside awesome_theme/.storybook)
```json
addons: [
    ...
    {
      name: '@storybook/addon-postcss',
      options: {
        postcssLoaderOptions: {
          implementation: require('postcss'),
        },
      },
    },
  ],
```
- Change `postcss.config.js` to be like below(inside awesome_theme)
```javascript
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```
- To run storybook just run `npm run develop`



## Troubleshooting
If you had an error that says `digital envelope routines::unsupported` just add change the theme `package.json` and add this line before calling the command `NODE_OPTIONS=--openssl-legacy-provider`
Below are the lines i had to change in order to make it work
```json
"build": "NODE_OPTIONS=--openssl-legacy-provider webpack --config ./webpack/webpack.prod.js --openssl-legacy-provider",
"develop": "concurrently --raw NODE_OPTIONS=--openssl-legacy-provider \"npm run webpack\" \"npm run storybook --openssl-legacy-provider\"",
"storybook": "NODE_OPTIONS=--openssl-legacy-provider start-storybook --ci -s ./dist,./images -p 6006",
"storybook-build": "NODE_OPTIONS=--openssl-legacy-provider npm run build && build-storybook -s ./dist,./images -o .out",
"storybook-deploy": "NODE_OPTIONS=--openssl-legacy-provider storybook-to-ghpages -o .out",
"webpack": "NODE_OPTIONS=--openssl-legacy-provider webpack --watch --config ./webpack/webpack.dev.js"
```

https://stackoverflow.com/questions/68020712/tailwind-css-classes-not-showing-in-storybook-build
https://git.drupalcode.org/project/tailwindcss/-/blob/5.x/package.json