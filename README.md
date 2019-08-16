# webpack javascript import for angularjs and laravel mix

Because we have spent HOURS finding the right way to import various library with webpack (the laravel mix breed of it), here is the cheat sheet.  It may not be state of the art programming, but module management in javascript is not standard, so here is what works for us.

* angularjs
* datatables.net
* JSZip
* bootstrap
* mxgraph (see at the bottom)
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

for mxGraph I had to crate a speical loader (the idea came from someone else on the internet)

```
export default angular
    .module("module.mxLoader", [])
    .service("mxLoader", mxLoader);

function mxLoader() {

    var mx;

    return {
        load: function loadF(mxModule) {

            if (!mx) {

                mx = mxgraph({
                    //mxImageBasePath: './images/mxgraph', 
                    mxLanguage: "en",
                    mxDefaultLanguage: "en"
                });

                // MUST load all the shit as global variables otherwise mxgraph can't find them (ex. mxCodec.decode will look for the class in the window object)
                "mxClient,mxLog,mxObjectIdentity,mxDictionary,mxResources,mxEffects,mxUtils,mxConstants,mxEvent,mxClipboard,mxUrlConverter,mxVmlCanvas2D,mxStencilRegistry,mxMarker,mxHierarchicalEdgeStyle,mxCellPath,mxPerimeter,mxEdgeStyle,mxStyleRegistry,mxCodecRegistry,mxGenericChangeCodec,mxStylesheetCodec,mxDefaultToolbarCodec,mxGraph,mxRubberband,mxHierarchicalLayout,mxFastOrganicLayout,mxGraphModel,mxPanningHandler,mxKeyHandler,mxParallelEdgeLayout,mxLayoutManager,mxCompactTreeLayout,mxPrintPreview,mxToolbar,mxOutline,mxCellTracker,mxCellOverlay,mxImage,mxLoadResources,mxPopupMenu,mxCylinder,mxRectangle,mxCellRenderer,mxVertexHandler,mxPoint,mxHandle,mxRhombus, mxActor,mxArrow,mxArrowConnector,mxCloud,mxConnector,mxConnector,mxEllipse,mxHexagon,mxImageShape,mxLabel,mxLine,mxPolyline,mxMarker,mxRectangleShape,mxShape,mxStencil,mxStencilRegistry,mxSwimlane,mxText,mxTriangle,mxAutoSaveManager,mxDivResizer,mxForm,mxGuide,mxImageBundle,mxImageExport,mxLog,mxMorphing,mxMouseEvent,mxPanningManager,mxSvgCanvas2D,mxUndoableEdit,mxUndoManager,mxUrlConverter,mxWindow,mxXmlCanvas2D,mxXmlRequest,mxCellEditor,mxCellState,mxCellStatePreview,mxConnectionConstraint,mxGraphSelectionModel,mxGraphView,mxMultiplicity,mxSwimlaneManager,mxTemporaryCellStates,mxGeometry,mxStackLayout,mxRadialTreeLayout,mxPartitionLayout,mxGraphLayout,mxEdgeLabelLayout,mxCompositeLayout,mxCircleLayout,mxSwimlaneOrdering,mxMinimumCycleRemover,mxMedianHybridCrossingReduction,mxHierarchicalLayoutStage,mxCoordinateAssignment,mxSwimlaneLayout,mxObjectCodec,mxGenericChangeCodec,mxTooltipHandler,mxSelectionCellsHandler,mxPopupMenuHandler,mxGraphHandler,mxElbowEdgeHandler,mxEdgeHandler,mxConstraintHandler,mxConnectionHandler,mxCellMarker,mxCellHighlight,mxDefaultPopupMenu,mxDefaultKeyHandler,mxCodec,mxGraphHierarchyModel,mxGraphAbstractHierarchyCell,mxGraphHierarchyEdge,mxGraphHierarchyNode,mxSwimlaneModel,mxEdgeSegmentHandler"
                    .split(",")
                    .forEach(function (module) {
                        window[module] = mx[module];
                    })


            }

            return mx[mxModule];
        }
    }

}
```

and then in each file where i need to use mxXYZ : 

```

export default angular
    .module("module.skGraph", [])
    .factory("skGraph", skGraph);

/* @ngInject */
function skGraph(mxLoader) {
    var mxCell = mxLoader.load("mxCell");
}
    
```
