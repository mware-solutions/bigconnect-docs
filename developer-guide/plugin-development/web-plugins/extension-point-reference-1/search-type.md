---
description: Add new types of search
---

# Search Type

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced)

Provide users with alternate search interfaces through a dropdown in the search pane. These additional interfaces have their own saved searches and completely control the interaction of search.

Search extensions control how the search is executed and the results are displayed.

![](../../../../.gitbook/assets/image%20%2858%29.png)

## Web Plugin

Register the plugin and a component/template for the new search interface.

```java
app.registerJavaScript("/com/mware/examples/search_advanced/plugin.js", true);
app.registerJavaScriptComponent("/com/mware/examples/search_advanced/React.jsx");
app.registerJavaScript("/com/mware/examples/search_advanced/flight.js", false);
app.registerJavaScriptTemplate("/com/mware/examples/search_advanced/template.hbs");
app.registerLess("/com/mware/examples/search_advanced/style.less");
app.registerWebWorkerJavaScript("/com/mware/examples/search_advanced/worker.js");
app.registerResourceBundle("/com/mware/examples/search_advanced/messages.properties");
```

[SearchAdvancedWebAppPlugin.java \(lines 24–30\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/java/com/mware/examples/search_advanced/SearchAdvancedWebAppPlugin.java#L24-L30)

The search extension requires a search URL used for saved searches, but we can use the built-in ones by just defining a new route. To access the route in the front-end we need to also add a services object in `worker.js`.

```java
//searches are saved by url
app.get("/com.mware/examples/search_advanced/flight/search", authenticator, csrfProtector, ReadPrivilegeFilter.class, ElementSearch.class);
app.post("/com.mware/examples/search_advanced/flight/search", authenticator, csrfProtector, ReadPrivilegeFilter.class, ElementSearch.class);
app.get("/com.mware/examples/search_advanced/react/search", authenticator, csrfProtector, ReadPrivilegeFilter.class, ElementSearch.class);
app.post("/com.mware/examples/search_advanced/react/search", authenticator, csrfProtector, ReadPrivilegeFilter.class, ElementSearch.class);
```

[SearchAdvancedWebAppPlugin.java \(lines 32–36\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/java/com/mware/examples/search_advanced/SearchAdvancedWebAppPlugin.java#L32-L36)

Defining a new services object extends what methods `dataRequest` can access.

```javascript
define('data/web-worker/services/com.mware-examples-search', [
    '../util/ajax'
], function(ajax) {
    'use strict';

    var api = {
            search: function(query, offset, size) {
                return ajax('GET', '/element/search', {
                    q: query,
                    filter: '[]',
                    offset: offset,
                    size: size
                })
            }
        };

    return api;
});
```

[worker.js](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/worker.js)

## Register Extension

Register the search extension pointing to your component. The `savedSearchUrl` points to the route created previously.

```javascript
bc.registry.registerExtension('org.bigconnect.search.advanced', {
    displayName: i18n('com.mware.examples.search.advanced.react.name'),
    componentPath: 'com/mware/examples/search_advanced/React',
    savedSearchUrl: '/com.mware/examples/search_advanced/react/search'
});
```

[plugin.js \(lines 3–7\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/plugin.js#L3-L7)

## Search Component

Create the component, it will be responsible for the UI, loading saved searches, executing searches, and displaying results.

In React:

```javascript
define([
    'public/v1/api',
    'create-react-class'
], function(
    bcApi,
    createReactClass) {
    'use strict';

    const SIZE = 20;

    const SearchExample = createReactClass({

        getInitialState() {
            const { initialParameters } = this.props;
            return { query: initialParameters && initialParameters.q || '' }
        },

        componentWillReceiveProps(nextProps) {
            if (nextProps.initialParameters && nextProps.initialParameters.q) {
                this.setState({
                    query: nextProps.initialParameters.q
                }, this.search);
            }
        },

        render() {
            return (
                <div className="com.mware-examples-advanced">
                  <div className="query-container">
                    <textarea placeholder="Query" onChange={this.onQueryChange} value={this.state.query}></textarea>
                  </div>
                  <div className="button-container">
                    <button className="btn btn-primary" onClick={this.onSearchClick}>Execute</button>
                  </div>
                </div>
            )
        },

        onSearchClick() {
            this.search();
        },

        onQueryChange(event) {
            this.setState({ query: event.target.value });

            this.props.setCurrentSearchForSaving({
                url: '/com.mware/examples/search_advanced/react/search',
                parameters: {
                    q: event.target.value
                }
            });
        },

        onInfiniteScrollRequest(resultsNode, data) {
            bcApi.connect()
                .then(({ dataRequest }) => {
                    dataRequest('com.mware-examples-search', 'search', this.state.query, data.paging.offset, SIZE)
                        .then((result) => {
                            $(resultsNode).trigger(
                                resultsNode,
                                'addInfiniteItems',
                                {
                                    success: true,
                                    items: result.elements,
                                    total: result.totalHits,
                                    nextOffset: result.nextOffset
                                }
                            );
                        })
                })
        },

        search() {
            bcApi.connect()
                .then(({ components, dataRequest }) => {
                    return Promise.all([
                        components.List,
                        dataRequest('com.mware-examples-search', 'search', this.state.query, 0, SIZE)
                    ]);
                })
                .spread((List, result) => {
                    this.props.updateQueryStatus({
                        success: true,
                        message: i18n('com.mware.examples.search.advanced.hits', result.totalHits)
                    });

                    this.props.renderResults(resultsNode => {
                        const content = $(resultsNode).css('display', result.totalHits ? 'block' : 'none')
                            .find('.content')
                            .teardownAllComponents()
                            .empty()

                        List.attachTo(content, {
                            items: result.elements,
                            usageContext: 'searchresults',
                            nextOffset: result.nextOffset,
                            infiniteScrolling: true,
                            total: result.totalHits
                        })

                        $(resultsNode).off('infiniteScrollRequest').on('infiniteScrollRequest', (event, data) => {
                            this.onInfiniteScrollRequest(resultsNode, data);
                        })
                    });
                })
                .catch(e => {
                    this.props.updateQueryStatus({ success: false, error: e });
                })
        }
    });

    return SearchExample;
});
// 98 lines hidden…
// 3 lines hidden…
```

[React.jsx \(lines 1–113\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/React.jsx#L1-L113)

In Flight:

```javascript
define([
    'public/v1/api',
    './template.hbs'
], function(
    api,
    template) {
    'use strict';

    var SIZE = 20;

    return api.defineComponent(SearchExample);

    function SearchExample() {

        this.attributes({
            textareaSelector: 'textarea',
            searchSelector: '.btn'
        })

        this.after('initialize', function() {
            this.on('click', {
                searchSelector: this.onSearch
            })
            this.on('change keyup', {
                textareaSelector: this.onChange
            })
            this.on('savedQuerySelected', this.onSavedQuerySelected);

            const query = this.attr.initialParameters && this.attr.initialParameters.q || '';
            this.$node.html(template({ query: query }));
        });

        this.onSavedQuerySelected = function(event, data) {
            this.select('textareaSelector').val(data.query.parameters.q);
            this.runSearch();
        };

        this.onInfiniteScrollRequest = function(event, data) {
            var self = this;

            this.dataRequest('com.mware-examples-search', 'search', this.query, data.paging.offset, SIZE)
                .then(function(result) {
                    self.trigger(
                        event.target,
                        'addInfiniteItems',
                        {
                            success: true,
                            items: result.elements,
                            total: result.totalHits,
                            nextOffset: result.nextOffset
                        }
                    );
                })
        };

        this.onChange = function(event) {
            this.trigger('setCurrentSearchForSaving', {
                url: '/com.mware/examples/search_advanced/flight/search',
                parameters: {
                    q: $(event.target).val()
                }
            })
        }

        this.onSearch = function(event) {
            this.runSearch();
        };

        this.runSearch = function() {
            var self = this,
                query = this.select('textareaSelector').val();

            this.query = query;

            api.connect().then(function(connected) {
                self.dataRequest = connected.dataRequest;

                return Promise.all([
                    connected.components.List,
                    connected.dataRequest('com.mware-examples-search', 'search', query, 0, SIZE)
                ])
            }).spread(function(List, result) {
                self.trigger('updateQueryStatus', {
                    success: true,
                    message: i18n('com.mware.examples.search.advanced.hits', result.totalHits)
                });

                self.trigger('renderResults', resultsNode => {
                    var resultsContainer = $(resultsNode).css('display', 'block')
                        .find('.content')
                        .teardownAllComponents()
                        .empty()

                    List.attachTo(resultsContainer, {
                        items: result.elements,
                        usageContext: 'searchresults',
                        nextOffset: result.nextOffset,
                        infiniteScrolling: true,
                        total: result.totalHits
                    })

                    resultsContainer.off('infiniteScrollRequest').on('infiniteScrollRequest', self.onInfiniteScrollRequest);
                });
            })
            .catch((e) => {
                self.trigger('updateQueryStatus', { success: false, error: e });
            });
        };

    }
});
// 96 lines hidden…
// 3 lines hidden…
```

[flight.js \(lines 1–111\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/flight.js#L1-L111)

## **Run Search and Display Results**

Using the service created earlier, we can make a data request to run the search and get the result as a promise. Using the public API we access the `List` component for display.

```javascript
bcApi.connect()
    .then(({ components, dataRequest }) => {
        return Promise.all([
            components.List,
            dataRequest('com.mware-examples-search', 'search', this.state.query, 0, SIZE)
        ]);
    })
    .spread((List, result) => {
```

[React.jsx \(lines 74–81\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/React.jsx#L74-L81)

The search results should be rendered in an element outside of the extension component. The search interface defines the DOM element of the container to use and provides that as an argument for a custom callback to render the results.

In React:

```javascript
this.props.renderResults(resultsNode => {
```

[React.jsx \(line 87\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/React.jsx#L87)

In Flight:

```javascript
self.trigger('renderResults', resultsNode => {
```

[flight.js \(line 88\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/flight.js#L88)

The container HTML is structured as follows:

```markup
<div class="" style="display:none">
    <div class="content">
        <!-- results content should be here -->
        <!-- or attach element list to "content" node -->
    </div>
</div>
```

To display the results, render the List component into the results containers' `.content` element, switch the `display` style on the container to show it, and enable `infiniteScrolling`.

```javascript
const content = $(resultsNode).css('display', result.totalHits ? 'block' : 'none')
    .find('.content')
    .teardownAllComponents()
    .empty()

List.attachTo(content, {
    items: result.elements,
    usageContext: 'searchresults',
    nextOffset: result.nextOffset,
    infiniteScrolling: true,
    total: result.totalHits
})
```

[React.jsx \(lines 88–99\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/React.jsx#L88-L99)

Then update the status of the query to display an error alert or show information such as the number of hits returned.

In React:

```javascript
this.props.updateQueryStatus({
    success: true,
    message: i18n('com.mware.examples.search.advanced.hits', result.totalHits)
});
```

[React.jsx \(lines 82–85\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/React.jsx#L82-L85)

In Flight:

```javascript
self.trigger('updateQueryStatus', {
    success: true,
    message: i18n('com.mware.examples.search.advanced.hits', result.totalHits)
});
```

[flight.js \(lines 83–86\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/flight.js#L83-L86)

**Infinite Scroll**

To finish making infinite scroll work, we listen for events on the results. Note, we have to listen using the container since events won't bubble up to the extension container as its not a descendant.

```javascript
$(resultsNode).off('infiniteScrollRequest').on('infiniteScrollRequest', (event, data) => {
    this.onInfiniteScrollRequest(resultsNode, data);
})
```

[React.jsx \(lines 101–103\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/React.jsx#L101-L103)

Then, when we get notified of scrolling, make another search request with the given offset, and trigger an update to the List element.

In React:

```javascript
onInfiniteScrollRequest(resultsNode, data) {
    bcApi.connect()
        .then(({ dataRequest }) => {
            dataRequest('com.mware-examples-search', 'search', this.state.query, data.paging.offset, SIZE)
                .then((result) => {
                    $(resultsNode).trigger(
                        resultsNode,
                        'addInfiniteItems',
                        {
                            success: true,
                            items: result.elements,
                            total: result.totalHits,
                            nextOffset: result.nextOffset
                        }
                    );
                })
        })
},
```

[React.jsx \(lines 54–71\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/React.jsx#L54-L71)

In Flight:

```javascript
this.onInfiniteScrollRequest = function(event, data) {
    var self = this;

    this.dataRequest('com.mware-examples-search', 'search', this.query, data.paging.offset, SIZE)
        .then(function(result) {
            self.trigger(
                event.target,
                'addInfiniteItems',
                {
                    success: true,
                    items: result.elements,
                    total: result.totalHits,
                    nextOffset: result.nextOffset
                }
            );
        })
};
```

[flight.js \(lines 38–54\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/flight.js#L38-L54)

## **Notify of Search Changes**

All search extensions should notify via `setCurrentSearchForSaving` that the search has modified. This allows the extension to work with the Saved Searches component to save the current search. The `url`should match the `savedSearchUrl` defined in the extension.

In React:

```javascript
onQueryChange(event) {
    this.setState({ query: event.target.value });

    this.props.setCurrentSearchForSaving({
        url: '/com.mware/examples/search_advanced/react/search',
        parameters: {
            q: event.target.value
        }
    });
},
```

[React.jsx \(lines 43–52\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/React.jsx#L43-L52)

In Flight:

```javascript
this.onChange = function(event) {
    this.trigger('setCurrentSearchForSaving', {
        url: '/com.mware/examples/search_advanced/flight/search',
        parameters: {
            q: $(event.target).val()
        }
    })
}
```

[flight.js \(lines 56–63\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-search-advanced/src/main/resources/com/mware/examples/search_advanced/flight.js#L56-L63)

