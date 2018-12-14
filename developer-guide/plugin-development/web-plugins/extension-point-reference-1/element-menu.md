---
description: Add new items to vertex or edge context menu
---

# Element Menu

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-element-context-menu)

Plugin to add new items to vertex or edge context menu. Providing a `shouldDisable` handler will still show the item in the context menu, provide a `canHandle` function if you want to remove items completely based on the current selection and target element.

To add a divider:

```javascript
registry.registerExtension('org.bigconnect.vertex.menu', 'DIVIDER');  // vertex menu
registry.registerExtension('org.bigconnect.edge.menu', 'DIVIDER');  //edge menu
```

## Example

To register an item:

```javascript
require(['public/v1/api'], function(bc) {

    bc.connect().then(function(api) {
        bc.registry.registerExtension('org.bigconnect.vertex.menu', {
            label: i18n('com.mware.examples.vertex.menu.google'),
            event: 'context-menu-search-google',
            selection: 1,
            shouldDisable: (selection, vertexId, DOMelement, vertex) => {
                return api.formatters.vertex.title(vertex) === i18n('vertex.property.title.not_available');
            },
            options: {
                insertIntoMenuItems: function(item, items) {
                    //add item under search submenu
                    var search = _.findWhere(items, { label: i18n('vertex.contextmenu.search') });
                    if (search && search.submenu) {
                        search.submenu.push(item);
                    } else {
                        items.push(item);
                    }
                }
            }
        });

        bc.registry.registerExtension('org.bigconnect.edge.menu', {
            label: i18n('com.mware.examples.edge.menu.delete'),
            event: 'context-menu-delete-edge',
            cls: 'context-menu-danger',
            options: {
                insertIntoMenuItems: function(item, items) {
                    // Add item to the end of the list
                    items.push(item);
                }
            }
        });

        $(document).on('context-menu-search-google', function(e, data) {
            api.dataRequest('vertex', 'store', {vertexIds: [data.vertexId]}).then(result => {
                var title = api.formatters.vertex.title(result[0]);
                var url = 'http://www.google.com/#safe=on&q=' + title;
                window.open(url, '_blank');
            });
        });

        $(document).on('context-menu-delete-edge', function(e, data) {
            data.edgeIds.forEach(edgeId => api.dataRequest('edge', 'delete', edgeId));
        });
    });
});
// 25 lines hidden…
```

[plugin.js \(lines 1–48\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-element-context-menu/src/main/resources/com/mware/examples/context_menu/plugin.js#L1-L48)

Then add an event listener to handle when your menu item is clicked:

```javascript
    bc.connect().then(function(api) {
        bc.registry.registerExtension('org.bigconnect.vertex.menu', {
            label: i18n('com.mware.examples.vertex.menu.google'),
            event: 'context-menu-search-google',
            selection: 1,
            shouldDisable: (selection, vertexId, DOMelement, vertex) => {
                return api.formatters.vertex.title(vertex) === i18n('vertex.property.title.not_available');
            },
            options: {
                insertIntoMenuItems: function(item, items) {
                    //add item under search submenu
                    var search = _.findWhere(items, { label: i18n('vertex.contextmenu.search') });
                    if (search && search.submenu) {
                        search.submenu.push(item);
                    } else {
                        items.push(item);
                    }
                }
            }
        });

        bc.registry.registerExtension('org.bigconnect.edge.menu', {
            label: i18n('com.mware.examples.edge.menu.delete'),
            event: 'context-menu-delete-edge',
            cls: 'context-menu-danger',
            options: {
                insertIntoMenuItems: function(item, items) {
                    // Add item to the end of the list
                    items.push(item);
                }
            }
        });

        $(document).on('context-menu-search-google', function(e, data) {
            api.dataRequest('vertex', 'store', {vertexIds: [data.vertexId]}).then(result => {
                var title = api.formatters.vertex.title(result[0]);
                var url = 'http://www.google.com/#safe=on&q=' + title;
                window.open(url, '_blank');
            });
        });

        $(document).on('context-menu-delete-edge', function(e, data) {
            data.edgeIds.forEach(edgeId => api.dataRequest('edge', 'delete', edgeId));
        });
    });
// 32 lines hidden…
```

[plugin.js \(lines 3–47\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-element-context-menu/src/main/resources/com/mware/examples/context_menu/plugin.js#L3-L47)

