# obvious.js
[![Coverage Status](https://coveralls.io/repos/github/ObviousJs/obvious-core/badge.svg?branch=master)](https://coveralls.io/github/ObviousJs/obvious-core?branch=master) [![release](https://img.shields.io/github/release/ObviousJs/obvious-core.svg)](https://github.com/ObviousJs/obvious-core/releases) [![lastCommit](https://img.shields.io/github/last-commit/ObviousJs/obvious-core)](https://github.com/ObviousJs/obvious-core/commits/master) [![](https://img.shields.io/badge/document-%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87-brightgreen)](https://github.com/ObviousJs/obvious-core/blob/master/README.zh.md)

Obvious is a progressive micro-front-end library. In the micro-front-end architecture, Obvious focuses on solving the  problem of orchestration and communication between micro frontend applications. It aims to help users quickly build a basic micro-front-end system and support deeper customization to achieve a complete and reliable micro-front-end architecture by providing easy-to-understand APIs and flexible middleware mechanisms.

## Features
- Provide flexible and convenient communication capabilities based on global state, event broadcast, and event unicast
- Support declaring dependencies when defining micro applications, and automatically activate dependencies when activating micro applications, allowing micro applications to be freely split and combine
- Have a flexible middleware mechanism which allows developers to freely customize the unified registration and loading rules of resources in the form of writing middleware to realize automatic registration. At the same time, allow developers to write middleware to control the code excuting, so that developers can elegantly access log, js sandbox and other functions
- Naturally supports loading multiple micro-applications in a single-screen page, based on which a high-end spa micro-front-end framework can be encapsulated, and the activation conditions of the micro-applications are completely freely set by the developer, no longer limited to routing hijacking.
- The concept is simple, the functional API is clear and easy to understand, and it can be developed without documentation

## Installation
npm: 

`npm install obvious-core`

umd:

`<script src="https://unpkg.com/obvious-core@{version}/dist/index.umd.js"></script>`

## Quick Start

In host enviroment, create a bus and declare the resource info
```js
import {createBus} from 'obvious-core';

const bus = createBus('host', {
    'react-app': {
        js: [
            'http://localhost:3000/static/js/bundle.js',
            'http://localhost:3000/static/js/0.chunk.js',
            'http://localhost:3000/static/js/main.chunk.js'
        ]
    },
    'vue-app': {
        js: [
            'http://localhost:8081/js/app.js',
            'http://localhost:8081/js/chunk-vendors.js'
        ]
    }
});
```

micro frontend application can get the bus, and create an App with it, at the same time, a socket can be created to communicate with other App

react-app
```js
import React from 'react';
import ReactDOM from 'react-dom';
import {getBus} from 'obvious-core';

const bus = getBus('host');
const socket = bus.createSocket();
bus.createApp('react-app')
    .bootstrap(async (config) => {
        socket.unicast('unicast-event');
        socket.broadcast('broadcast-event');
        socket.initState('someState', true);
        ReactDOM.render(<App />, document.querySelector(config.mountPoint));
    });
```  

vue-app
```js
import Vue from 'vue';
import App from './App.vue';
import {getBus} from 'obvious-core';

Vue.config.productionTip = false;

const bus = getBus('host');
const socket = bus.createSocket();
bus.createApp('vue-app')
    .bootstrap(async (config) => {
        socket.onUnicast('unicast-event', () => {
            // do something
        });
        socket.onBroadcast('broadcast-event', () => {
            // do something
        });
        socket.setState('someState.sub.prop.array', [])
        socket.watchState('someState.sub.prop.array[0]', (val) => {
            // do something
        });
        new Vue({
            render: h => h(App),
        }).$mount(config.mountPoint);
    });
```

In host enviroment, activate the application
```js
bus.activateApp('react-app', {mountPoint: document.getElementById('#react-app')});
bus.activateApp('vue-app', {mountPoint: document.getElementById('#vue-app')});
```

## Example
![](docs/_media/tutorial-target.gif)

```
npm run demo:install
npm run demo:react
npm run demo:vue
npm run demo:host
```

## Document

[obvious.js: the progressive micro frontend framework](https://obviousjs.github.io/obvious-core/#/)

## License
obvious is [MIT Licensed](https://github.com/ObviousJs/obvious-core/blob/master/LICENSE)
