# Using Tailwind with Laravel Mix


## Laravel Mix

Begin by installing Laravel Mix through NPM, and then copying the example Mix file to your project root. Make sure to change `my-project` to the actual name of your project.

    mkdir my-project && cd my-project
    npm init -y
    npm install laravel-mix --save-dev
    cp node_modules/laravel-mix/setup/webpack.mix.js ./

You should now have the following directory structure:

    node_modules/
    package.json
    webpack.mix.js

Head over to your `webpack.mix.js` file and replace the contents with the following code:

    let mix = require('laravel-mix');
    
    mix.js('src/js/app.js', 'dist/js')
       .sass('src/sass/app.scss', 'dist/css');

Take note of the source paths and create the necessary `app.js` and `app.scss` files.

## Tailwind CSS & PurgeCSS

Install Tailwind CSS via npm:

    npm install tailwindcss --save-dev

Install PurgeCSS via npm:

    npm install laravel-mix-purgecss --save-dev

Create the Tailwind config file:

    ./node_modules/.bin/tailwind init

If the above command will not work because running scripts is disabled on your Windows system, run the following command in PowerShell (as Administrator):

    Set-ExecutionPolicy -ExecutionPolicy Unrestricted

In the `src/sass` folder, create a new file named `_tailwind.scss`
Put this code inside the newly created file:

    @tailwind base;
    @tailwind components;
    @tailwind utilities;

Open `app.scss` and import the previously created `_tailwind.scss`:

    @import 'tailwind';

Open the `webpack.mix.js` file and replace the contents with the following code:

    let mix = require('laravel-mix');
    let tailwindcss = require('tailwindcss');
    require('laravel-mix-purgecss');

    mix.js('src/js/app.js', 'dist/js')
       .sass('src/sass/app.scss', 'dist/css')
       .options({
            processCssUrls: false,
            postCss: [ tailwindcss('./tailwind.config.js') ],
       })
       .purgeCss({
           enabled: true,
           content: [
               './dist/**/*.htm',
               './dist/**/*.html',
               './dist/**/*.js',
               './dist/**/*.jsx',
               './dist/**/*.php',
               './dist/**/*.vue',
           ],
           extensions: ['htm', 'html', 'js', 'php', 'vue'],
       });

Add the following NPM scripts to your `package.json` file to speed up your workflow.

    "scripts": {
        "dev": "npm run development",
        "development": "cross-env NODE_ENV=development node_modules/webpack/bin/webpack.js --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
        "watch": "npm run development -- --watch",
        "hot": "cross-env NODE_ENV=development node_modules/webpack-dev-server/bin/webpack-dev-server.js --inline --hot --config=node_modules/laravel-mix/setup/webpack.config.js",
        "prod": "npm run production",
        "production": "cross-env NODE_ENV=production node_modules/webpack/bin/webpack.js --no-progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js"
    }

Lastly, to handle different environments, these scripts make use of `cross-env`:

    npm install cross-env --save-dev

Run the following command to test if everything is working:

    npm run dev
