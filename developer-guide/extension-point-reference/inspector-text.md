---
description: Replace the element inspector text section
---

# Inspector Text

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-detail-text)

Replace the default text collapsible section content in the Inspector.

{% hint style="warning" %}
The console will show a warning if multiple extensions are found for a given vertex, name, and key. The extension used is non-deterministic.
{% endhint %}

![](../../.gitbook/assets/image.png)

## Web Plugin

Register the resources needed.

```java
app.registerJavaScript("/com/mware/examples/detail_text/plugin.js", true);
app.registerJavaScriptComponent("/com/mware/examples/detail_text/Example.jsx");
app.registerResourceBundle("/com/mware/examples/detail_text/messages.properties");
```

[DetailTextWebAppPlugin.java \(lines 16–18\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-detail-text/src/main/java/com/mware/examples/detail_text/DetailTextWebAppPlugin.java#L16-L18)

## Register Extension

Now, register the text extension for all text properties.

```javascript
bc.registry.registerExtension('org.bigconnect.detail.text', {
    shouldReplaceTextSectionForVertex: function(vertex, name, key) {
        return true;
    },
    componentPath: 'com/mware/examples/detail_text/Example'
});
```

[plugin.js \(lines 3–8\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-detail-text/src/main/resources/com/mware/examples/detail_text/plugin.js#L3-L8)

## The JS Component

The component can be React or Flight, here is a React example that prints the name, key pair.

```javascript
render() {
    const { vertex, propertyName, propertyKey } = this.props;
    return (
        <dl>
            <dt>Name</dt>
            <dd><code>{propertyName}</code></dd>
            <dt>Key</dt>
            <dd><code>{propertyKey}</code></dd>
        </dl>
    )
}
```

[Example.jsx \(lines 7–17\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-detail-text/src/main/resources/com/mware/examples/detail_text/Example.jsx#L7-L17)



