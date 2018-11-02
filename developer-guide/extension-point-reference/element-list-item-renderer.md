---
description: Adjust how list items are displayed
---

# Element List Item Renderer

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-entity-listitemrenderer)

This allows plugins to adjust how list items are displayed in search results, Inspector, or anywhere else the Element List Component is used.

![](../../.gitbook/assets/image%20%283%29.png)

## Web Plugin

Register the resources needed.

```java
app.registerJavaScript("/com/mware/examples/entity_listitemrenderer/plugin.js", true);
app.registerJavaScript("/com/mware/examples/entity_listitemrenderer/component.js", false);
app.registerJavaScriptTemplate("/com/mware/examples/entity_listitemrenderer/template.hbs");
app.registerResourceBundle("/com/mware/examples/entity_listitemrenderer/messages.properties");
app.registerLess("/com/mware/examples/entity_listitemrenderer/style.less");
```

[EntityListItemRendererWebAppPlugin.java \(lines 16–20\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-entity-listitemrenderer/src/main/java/com/mware/examples/entity_listitemrenderer/EntityListItemRendererWebAppPlugin.java#L16-L20)

## Register Extension

Now, register the item renderer. It will only override when the `usageContext` is `searchresults`. Some contexts send different input as the `item` parameter, so its recommended to accept specific contexts, rather than support all contexts.

```javascript
bc.registry.registerExtension('org.bigconnect.entity.listItemRenderer', {
    canHandle: function(element, usageContext) {
        return usageContext === 'searchresults';
    },
    componentPath: 'com/mware/examples/entity_listitemrenderer/component'
});
```

[plugin.js \(lines 3–8\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-entity-listitemrenderer/src/main/resources/com/mware/examples/entity_listitemrenderer/plugin.js#L3-L8)

## Create the JS Component

Create a Flight component that will render each row.

```javascript
define([
    'public/v1/api',
    './template.hbs'
], function(
    api,
    template) {

    return api.defineComponent(ListItemExample);

    function ListItemExample() {

        this.attributes({
            item: null,
            usageContext: null,
            imgSelector: '.img'
        });

        this.after('initialize', function() {
            var $node = this.$node;
            var item = this.attr.item;
            var usageContext = this.attr.usageContext;

            api.connect().then(function(connected) {
                $node
                    .addClass('example-renderer')
                    .html(template({
                        item: item,
                        usageContext: usageContext,
                        title: connected.formatters.vertex.title(item)
                    }));
            })

            this.on('loadPreview', _.once(this.onLoadPreview.bind(this)));

            $node.data('vertexId', item.id);
        });

        this.onLoadPreview = function(event) {
            var self = this,
                item = this.attr.item;

            api.connect().then(function(c) {
                var url = c.formatters.vertex.image(item, null, 80, 80);
                var imageIsFromConcept = c.formatters.vertex.imageIsFromConcept(item);

                self.select('imgSelector')
                    .toggleClass('icon', imageIsFromConcept)
                    .css('background-image', 'url(' + url + ')');
            })
        };
    }
});
```

[component.js](https://github.com/mware-solutions/doc-examples/blob/master/extension-entity-listitemrenderer/src/main/resources/com/mware/examples/entity_listitemrenderer/component.js)

Render the template using the item, and `formatters.vertex.title` to get the title.

```javascript
.html(template({
    item: item,
    usageContext: usageContext,
    title: connected.formatters.vertex.title(item)
}));
```

[component.js \(lines 26–30\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-entity-listitemrenderer/src/main/resources/com/mware/examples/entity_listitemrenderer/component.js#L26-L30)

Remember to set the `vertexId` \(or `edgeId`\) in data of the element, for selection to work correctly.

```javascript
$node.data('vertexId', item.id);
```

[component.js \(line 35\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-entity-listitemrenderer/src/main/resources/com/mware/examples/entity_listitemrenderer/component.js#L35)

To display an image in the row, wait for the `loadPreview` event that notifies the component that it has scrolled into view. As this event might be called many times, we ensure `onLoadPreview` is only ever called once using underscore.js `_.once`.

```javascript
this.on('loadPreview', _.once(this.onLoadPreview.bind(this)));
```

[component.js \(line 33\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-entity-listitemrenderer/src/main/resources/com/mware/examples/entity_listitemrenderer/component.js#L33)

The image path returned from `formatters.vertex.image` function might be the concept icon, check if it using `formatters.vertex.imageIsFromConcept` is so we can style it differently.

```javascript
this.onLoadPreview = function(event) {
    var self = this,
        item = this.attr.item;

    api.connect().then(function(c) {
        var url = c.formatters.vertex.image(item, null, 80, 80);
        var imageIsFromConcept = c.formatters.vertex.imageIsFromConcept(item);

        self.select('imgSelector')
            .toggleClass('icon', imageIsFromConcept)
            .css('background-image', 'url(' + url + ')');
    })
};
```

[component.js \(lines 38–50\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-entity-listitemrenderer/src/main/resources/com/mware/examples/entity_listitemrenderer/component.js#L38-L50)

#### Custom CSS <a id="custom-css"></a>

To customize styling, add a class to the `node`.

```javascript
.addClass('example-renderer')
```

[component.js \(line 25\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-entity-listitemrenderer/src/main/resources/com/mware/examples/entity_listitemrenderer/component.js#L25)

```css
.element-list ul li a.example-renderer {
    height: auto;
    min-height: 3em;
    padding: 0.5em 1em;
    display: flex;
    align-items: center;

    .img {
        @size: 40px;
        flex: 0 0 auto;
        width: @size;
        height: @size;
        border-radius: @size / 2;
        background-position: center;
        background-size: contain;
        margin-left: 0.5em;
        border: 1px solid #e2e2e2;

        &.icon {
            border-radius: 0;
            border-width: 0;
            background-size: 50% auto;
            background-repeat: no-repeat;
        }
    }
    .title {
        flex: 1 1 auto;
    }
}
// 5 lines hidden…
```

[style.less \(lines 1–29\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-entity-listitemrenderer/src/main/resources/com/mware/examples/entity_listitemrenderer/style.less#L1-L29)

When the row is selected the list element will have an `active` class. Certain elements may need to adjust to be visible with the blue selection background. The example changes the image border color:

```css
.element-list ul li.active a.example-renderer {
    .img {
        border-color: #096d9e;
    }
}
```

[style.less \(lines 31–35\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-entity-listitemrenderer/src/main/resources/com/mware/examples/entity_listitemrenderer/style.less#L31-L35)

