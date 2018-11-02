---
description: Change how the visibility is displayed and edited
---

# Visibility

[Example code](https://github.com/mware-solutions/doc-examples/tree/master/extension-visibility)

Plugin to configure the user interface for displaying and editing visibility authorization strings.

The visibility component requires two FlightJS components registered for viewing and editing:

```javascript
registry.registerExtension('org.bigconnect.visibility', {
    editorComponentPath: 'myEditor',
    viewerComponentPath: 'myViewer'
});
```

### Visibility Editor Component <a id="visibility-editor-component"></a>

Describes the form for editing visibility values.

#### Attributes <a id="attributes"></a>

Accessible in plugin as `this.attr`

* `value`: Previous value to populate.

#### Events <a id="events"></a>

[Visibility](http://localhost/GLOSSARY.html#visibility) plugins event contract.

**Must Respond to:**

* `visibilityclear`: Clears the current value.

```javascript
this.on('visibilityclear', function() {
    // Clear the value
    this.select('fieldSelector').val('');
});
```

**Must Fire:**

* `visibilitychange`: When the value changes. Send `valid` boolean if the current value is valid for submitting.

```javascript
this.trigger("visibilitychange", {
    value: "[current value]",
    valid: [ true | false ]
});
```

### Visibility Display Component <a id="visibility-display-component"></a>

Describes the display of visibility values.

#### Attributes <a id="attributes"></a>

* `value`: Current visibility value.
* `property`: Current property.



