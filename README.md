# Laravel-React_Intertia_Setup


If you want to create a new Laravel application that will use React for client-side, and everything will be only on one server, this article is for you. Today I’m gonna show how you can set up a new Laravel application that will use React with Inertia.js. So let’s begin🤘

Create application
First of all, let’s create a new laravel application:

$ composer create-project laravel/laravel react-inertia-app
# Or you can just use Laravel CLI
$ laravel new react-inertia-app
$ cd react-inertia-app

Server-side setup
Now when we created a new laravel application, it’s time to install everything that we need for the server-side part of the app and configure it. So, let’s install the Inertia.js composer dependency:

```$ composer require inertiajs/inertia-laravel```

Rename file resources/app/welcome.blade.php to the app.blade.php and paste the next code inside:

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Laravel</title>
        @viteReactRefresh 
        @vite(['resources/css/app.css', 'resources/js/app.jsx'])
        <!-- As you can see, we will use vite with jsx syntax for React-->
        @inertiaHead
    </head>
    <body>
        @inertia
    </body>
</html>
```

After that we need to generate a middleware for Inertia by using the next command:

```$ php artisan inertia:middleware```

And to use the middleware that we just generated, open the app/Http/Kernel.php file and go to the web middleware group and add generated middleware to the web group:

```
<?php

// ...
'web' => [
    // ...
    \App\Http\Middleware\HandleInertiaRequests::class,
],
// ...
```

That’s all that we need for server-side setup, now let’s move on to the client side.

Client-side setup
Before we are going to write a first React component — we need to install all dependencies that we need for this tutorial:

```$ npm install @inertiajs/inertia-react @inertiajs/react @vitejs/plugin-react react react-dom```

After installation, we will need to configure Vite in our application so let’s do this in vite.config.js:

```
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import react from '@vitejs/plugin-react';

export default defineConfig({
    plugins: [
        react(), // React plugin that we installed for vite.js
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
});
```

Okay, and now we can configure the app.js file, but first let’s rename this file to the app.jsx:

```
import React from 'react'
import {createRoot} from 'react-dom/client'
import {createInertiaApp } from '@inertiajs/inertia-react'
import {resolvePageComponent} from 'laravel-vite-plugin/inertia-helpers'

createInertiaApp({
    // Below you can see that we are going to get all React components from resources/js/Pages folder
    resolve: (name) => resolvePageComponent(`./Pages/${name}.jsx`,import.meta.glob('./Pages/**/*.jsx')),
    setup({ el, App, props }) {
        createRoot(el).render(<App {...props} />)
    },
})

```

Now we need to create a “Pages” folder inside the js folder, and inside this folder let’s create a test component named “Test.jsx”:
```
import React, { useState } from 'react';

const Test = () => {
    return (
        <h1>This is test component</h1>
    )
}

export default Test
```

Render component
We configured everything that we need to use React in our application and now it’s time to render the component that we just created, so let’s open routes/web.php and let’s try to render Test.jsx on the homepage:

```
<?php

use Illuminate\Support\Facades\Route;
use Inertia\Inertia; // We are going to use this class to render React components

Route::get('/', function () {
    return Inertia::render('Test'); // This will get component Test.jsx from the resources/js/Pages/Test.jsx
});
```

Okay, and the final step — run the local server and run vite in the terminal:

```$ php artisan serve```
or
```$ npm run dev```
If your using laravel valet go to projectname.test if not go to the localhost:8000 or the route specified in your terminal and if you see the content of the test component, that means that everything works successfully
