# @esri/cedar

[![Build Status](https://travis-ci.org/Esri/cedar.svg?branch=master)](https://travis-ci.org/Esri/cedar)

Cedar is a library for crafting and sharing data visualizations powered by ArcGIS Services.

> You are looking at the documentation for cedar v1.x, which is currently in beta. You can also view the [v0.x documentation](https://github.com/Esri/cedar/blob/v0.x/README.md).

[View live examples of how to use cedar](http://cedar-v1.surge.sh/)

<!--
At the highest level, Cedar provides a simple chart API. Beyond that it is possible to create new and unique chart types that can be loaded and customized through interactions and styling depending on your needs.
-->

**Currently Esri Cedar is in development and should be thought of as a beta or preview.**

## Types of Charts

Cedar currently provides a set of commonly used chart types including `bar`, `line`, `area`, and `pie`, `scatter`, and `radar`. In the future it will be possible for developers to create unique and custom charts that can be used by other developers with new data sources.
<!--
When starting with Cedar, we suggest that you begin by exploring the simple charts using your own data services. As you experiment with the interactions with Maps and more complex interaction you can also customize these charts with new capabilities such as legends, size scaling or labeling. Finally, you can fork and create completely custom chart templates that you then provide for other developers to use through Cedar.
-->

## Getting Started

### Installing Cedar

You can install cedar and it's [dependencies](#dependencies) from npm:
```bash
npm install @esri/cedar
```

Alternatively, you can get cedar from the [unpkg.com](https://unpkg.com/) CDN as shown below.

### Loading Cedar

You can load Cedar and its dependencies by including script tags that point to the CDN or your locally installed versions of these libraries. This will make the `cedar` global available to your application.

```html
<!-- load the amCharts base library -->
<script src="https://www.amcharts.com/lib/3/amcharts.js"></script>
<!-- in this case, we only need bar charts, so we'll load the appropriate amCharts script -->
<script src="https://www.amcharts.com/lib/3/serial.js"></script>
<!-- load the arcgis-rest-js scripts -->
<script src="https://unpkg.com/@esri/arcgis-rest-request@1.7.1/dist/umd/request.umd.min.js"></script>
<script src="https://unpkg.com/@esri/arcgis-rest-feature-service@1.7.1/dist/umd/feature-service.umd.min.js"></script>
<!-- optionally load an amcharts theme; cedar provides a calcite theme -->
<script src="https://unpkg.com/@esri/cedar/dist/umd/themes/amCharts/calcite.js"></script>
<!-- load cedar -->
<script src="https://unpkg.com/@esri/cedar/dist/umd/cedar.js"></script>
```

If you need to use other chart types, or want to use amCharts plugins, load the appropriate amCharts scripts before loading cedar:

```html
<!-- for pie charts -->
<script src="https://www.amcharts.com/lib/3/pie.js"></script>
<!-- for scatter and bubble charts -->
<script src="https://www.amcharts.com/lib/3/xy.js"></script>
<!-- for radar charts -->
<script src="https://www.amcharts.com/lib/3/radar.js"></script>
<!-- optioinally load the amcharts plugin to export the chart as and image or table -->
<script src="https://www.amcharts.com/lib/3/plugins/export/export.min.js"></script>
<link rel="stylesheet" href="https://www.amcharts.com/lib/3/plugins/export/export.css" type="text/css" media="all" />
```

### Using Cedar

Once cedar is loaded you can create and show the chart at a designated element. First create the element:

```html
<div id="chart" style="height: 400px;"></div>
```

Then add a script that will configure cedar and render the chart:

```html
<script>
  // connect to the data
  var datasets = [{
    "url": "https://services.arcgis.com/uDTUpUPbk8X8mXwl/arcgis/rest/services/Public_Schools_in_Onondaga_County/FeatureServer/0",
    "name": "schools",
    "query": {
      "orderByFields": "Number_of_SUM DESC",
      "groupByFieldsForStatistics": "Type",
      "outStatistics": [{
        "statisticType": "sum",
        "onStatisticField": "Number_of",
        "outStatisticFieldName": "Number_of_SUM"
      }]
    }
  }];

  // designate a one or more series to show the data on the chart
  var series = [{
    "category": {"field": "Type", "label": "Type"},
    "value": {"field": "Number_of_SUM", "label": "Number of Students"},
    "source": "schools"
  }];

  // optionally override any of the cart type's default styles
  var overrides = {
    "categoryAxis": {
      "labelRotation": -45
    }
  }

  //create a cedar chart using the known 'bar' type
  var elementId = 'chart';
  var chart = new cedar.Chart(elementId, {"type": "bar"})
    .datasets(datasets)
    .series(series)
    .overrides(overrides);

  // render the chart
  chart.show();
</script>
```

### Configuring Cedar
You can configure cedar to use a custom implementation of `fetch()` by setting `cedar.config.fetch = myCustomFetch`.

## Demos

See [this code pen](https://codepen.io/tomwayson/pen/paxgeO) to try creating a simple bar chart like the one above.

You can then [see and modify the definitions for different types of charts](http://cedar-v1.surge.sh/).

You can also see how to use cedar with an ArcGIS API for JavaScript map in these examples:
- [A chart that aggregates map data](https://codepen.io/tomwayson/pen/YaKGjZ)
- [A chart using layer features as inline data](https://codepen.io/tomwayson/pen/mxdVqO)

## Components of a Cedar Chart

Cedar charts are defined by the following ingredients:

- an array of `datasets`, each has, either:
 - a `url` to an ArcGIS feature yayer along with optional `query` parameters;
 - ...or inline `data`, which can be a [feature set](https://esri.github.io/arcgis-rest-js/api/common-types/IFeatureSet/), or an array of [features](https://esri.github.io/arcgis-rest-js/api/common-types/IFeature/) or [POJO](http://blog.dreasgrech.com/2012/02/creating-pojos-in-javascript.html)s
- an array of `series` that bind the data to the bars, lines, points, etc on the chart
- and `overrides` are specific modifications to the cart type's default styles

<!-- TODO: API docs -->
<!-- See the [API documentation](http://esri.github.io/cedar/api) for further details. -->

### Development Instructions

This repository is a monorepo managed using [yarn workspaces](https://yarnpkg.com/lang/en/docs/workspaces/) and [lerna](https://github.com/lerna/lerna)

1. Fork this repository and clone 'cedar' locally
1. `cd` into the `cedar` folder
1. Install the dependencies and initialize the monorepo with `yarn`
1. to run the docs site locally, start a web server at the root folder and visit `/docs`
1. to rebuild the script files used by the docs page whenever the source code is updated, run `yarn start`

### Tests

To run tests one time for all packages, run `yarn test` from the monorepo root.

To run tests continually for any package as you update it's soruce code, `cd` into that package and run `yarn run test:watch` to continually run that package's tests as you update the source code

### Dependencies

Cedar is a very thin wrapper around other libraries that do the heavy lifting. Cedar uses the [amCharts JavaScripts Charts](https://www.amcharts.com/javascript-charts/) library as it's charting engine. Cedar also uses [@esri/arcgis-rest-feature-service](https://esri.github.io/arcgis-rest-js/api/feature-service/) and [@esri/arcgis-rest-request](https://esri.github.io/arcgis-rest-js/api/request/) to query feature data. You will need to include these libraries along with cedar in your application. If you [install cedar using npm or yarn](#installing-cedar) these libraries will be installed for you, but you will have to make sure that your module bundler can resolve and include them in your application. If you are loading cedar from the CDN, please refer to the [loading cedar](#loading-cedar) section above for the `<script>` tags that you will need to include.

Cedar supports the [same browsers as ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/reference/browsers.htm), however you may need to include polyfills for [`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) and [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise), if your application has to support browers that don't support [fetch](https://caniuse.com/#search=fetch) or [Promise](https://caniuse.com/#search=promise) (i.e. IE or older versions of Safari/Android).

### Versioning

For transparency into the release cycle and in striving to maintain backward compatibility, Cedar is maintained under the Semantic Versioning guidelines and will adhere to these rules whenever possible.

Releases will be numbered with the following format:

`<major>.<minor>.<patch>`

And constructed with the following guidelines:

* Breaking backward compatibility **bumps the major** while resetting minor and patch
* New additions without breaking backward compatibility **bumps the minor** while resetting the patch
* Bug fixes and misc changes **bumps only the patch**

For more information on SemVer, please visit <http://semver.org/>.


### Licensing
Copyright 2018 Esri

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

> http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

A copy of the license is available in the repository's [LICENSE](./LICENSE) file.
