# webpack javascript import for angularjs and laravel mix

Because we have spent HOURS finding the right way to import various library with webpack (the laravel mix breed of it), here is the cheat sheet.  It may not be state of the art programming, but module management in javascript is not standard, so here is what works for us.

* angularjs
* datatables.net
* JSZip
* bootstrap
* mxgraph
* moments js
* json-api-normalize
* angular-marked
* chartjs
* ngIdle
* ...

create a `bootstrap.js` file for bootstrap, datatables and mxgrpah


```
try {
    window.Popper = require('popper.js').default;
    window.$ = window.jQuery = require('jquery');
    require('angular');

    window.JSZip = require('jszip')

    require('datatables.net');
    require('datatables.net-bs');
    require('datatables.net-buttons');
    require('datatables.net-buttons/js/buttons.colVis.js');
    require('datatables.net-buttons/js/buttons.html5.js');
    require('datatables.net-buttons/js/buttons.flash.js');
    require('datatables.net-buttons/js/buttons.print.js');
    require('datatables.net-fixedheader');
    require('datatables.net-scroller');
    require('datatables.net-colreorder');

    require('bootstrap');
    require('bootstrap-3-typeahead');

    window.jsonApiNormalize = require('json-api-normalize');

} catch (e) { }

window.mxgraph = require('mxgraph');
```

`app.js` (the first file that kicks in)


```
import "@babel/polyfill";

require('./bootstrap');

import angular from 'angular';

import AppCore from './core/core.module';
// and other modules but core is most important here, see below

export default angular.module('app', [
    
    AppCore.name,
    // all other modules

])
```


`webpack.mix.js`

```
mix.webpackConfig({
    module: {
        rules: [
            { test: /\.sass$/, loaders: ['style-loader', 'css-loader', 'sass-loader'] },
            { test: /\.html$/, loader: 'html-loader' },
        ]
    },
    plugins: [
        new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/),
        new ngAnnotatePlugin({
            add: true,
        }),
        new WebpackWatchRunPlugin({
            ignored: ["node_modules"]
        })
    ],
    stats: "verbose"
}).js('resources/app/app.js', 'public/js')
    // .eslint({
    //     fix: false,
    //     cache: false,
    //     //...
    // })
    .babel('public/js/app.js', 'public/js/app.es5.js')
    .extract()
    .autoload({
        jquery: ['$', 'window.jQuery', 'jQuery', 'jquery'],
        DataTable: 'datatables.net-bs'
    })

```

`core.module.js`

```

import 'angular-marked'
import 'angular-datatables';
import 'angular-datatables/dist/plugins/buttons/angular-datatables.buttons';
import 'angular-datatables/dist/plugins/fixedheader/angular-datatables.fixedheader';
import 'angular-datatables/dist/plugins/scroller/angular-datatables.scroller';
import 'angular-datatables/dist/plugins/columnfilter/angular-datatables.columnfilter';
import 'angular-datatables/dist/plugins/colreorder/angular-datatables.colreorder';

import 'ng-idle';

import 'angular-material';
import 'angular-messages';
import 'angular-sanitize';
import 'angular-cookies';
import ngClipboard from 'angular-clipboard';
import 'chart.js';
import 'angular-chart.js';

import 'ngstorage';
import 'ng-flow-webpack';
import 'angular-loading-bar';
import ie11warningComponent from '../components/modals/ie11warning/ie11warning.component';

export default angular.module('app.core', [
    /*
     * Angular modules
     */
    'ngMaterial',
    'ngMessages',
    'ngSanitize',
    'ngCookies',

    /*
     * 3rd Party modules
     */
    'flow',
    ngClipboard.name,
    'chart.js',
    'ngStorage',
    'hc.marked',
    'datatables',
    // 'md.data.table',
    'datatables.buttons',
    'datatables.scroller',
    'datatables.fixedheader',
    'datatables.columnfilter',
    'datatables.colreorder',
    'ngIdle',
    'angular-loading-bar'
]);

```
