---
description: Add new items to the map options menu
---

# Map Options

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-map-options)

Add custom options components \(Flight or React\) which display in the map options menu \(next to Fit\).

![](http://localhost/extension-points/front-end/mapOptions/options.png)

## Web Plugin

Register the plugin, and component scripts. Then register a file reference to the [geojson](https://github.com/mware-solutions/doc-examples/blob/master/extension-map-options/src/main/resources/com/mware/examples/map_options/countries.geojson) of countries.

```java
app.registerJavaScript("/com/mware/examples/map_options/plugin.js", true);
app.registerJavaScriptComponent("/com/mware/examples/map_options/CountryBorders.jsx");
app.registerFile("/com/mware/examples/map_options/countries.geojson", "application/vnd.geo+json");
app.registerResourceBundle("/com/mware/examples/map_options/messages.properties");
```

[MapOptionsWebAppPlugin.java \(lines 16–19\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-map-options/src/main/java/com/mware/examples/map_options/MapOptionsWebAppPlugin.java#L16-L19)

## Register Extension

Register the map options extension and point the path to the React component.

```javascript
bc.registry.registerExtension('org.bigconnect.map.options', {
    identifier: 'com.mware-examples-map_options-countryborders',
    optionComponentPath: 'com/mware/examples/map_options/CountryBorders'
});
```

[plugin.js \(lines 3–6\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-map-options/src/main/resources/com/mware/examples/map_options/plugin.js#L3-L6)

## Component

The react component manages the state of the geojson layer \(visible/hidden\) using a user preference, and uses the OpenLayers API to add/remove the vector layer.

```javascript
define(['public/v1/api', 'create-react-class'], function(bc, createReactClass) {
    const key = 'examples-geojson';
    const CountryBorders = createReactClass({
        getInitialState() {
            const visible = bcData.currentUser.uiPreferences[key] === 'true'
            return { visible }
        },
        componentDidMount() {
            this.createCountryLayer();
        },
        render() {
            const { visible } = this.state;

            return (
                <label>
                    Toggle Country Borders
                    <input type="checkbox" checked={visible} onChange={this.onChange} />
                </label>
            )
        },
        onChange() {
            const { visible } = this.state;
            const newVal = !visible;

            if (visible) {
                this.props.map.removeLayer(this.layer);
            } else {
                this.props.map.addLayer(this.layer);
            }

            bcData.currentUser.uiPreferences[key] = String(newVal);
            bc.connect()
                .then(c => c.dataRequest('user', 'preference', key, newVal));

            this.setState({ visible: newVal })
        },
        createCountryLayer() {
            if (this.layer) return;
            var { ol, map, cluster } = this.props;
            var styleArray = [new ol.style.Style({
              stroke: new ol.style.Stroke({
                color: 'red',
                width: 2
              })
            })];

            var vector = new ol.layer.Vector({
                source: new ol.source.Vector({
                  url: 'com.mware/examples/map_options/countries.geojson',
                  format: new ol.format.GeoJSON()
                }),
                style: function(feature, resolution) {
                  return styleArray;
                }
              });

            if (this.state.visible) {
                map.addLayer(vector);
            }
            this.layer = vector;
        }
    });

    return CountryBorders;
});
```

[CountryBorders.jsx](https://github.com/mware-solutions/doc-examples/blob/master/extension-map-options/src/main/resources/com/mware/examples/map_options/CountryBorders.jsx)

