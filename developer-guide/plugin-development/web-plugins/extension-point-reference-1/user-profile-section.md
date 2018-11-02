---
description: Add new user profile sections
---

# User Profile Section

Plugin to configure new sections for the  user profile dialog.

To register a section:

```javascript
require(['public/v1/api'], function(registry) {
    registry.registerExtension('org.bigconnect.user.account.page', {
        identifier: 'changePassword',
        pageComponentPath: 'com.mware.useraccount.changePassword'
    });

    define('com.mware.useraccount.changePassword', [
        'flight/lib/component'
    ], function(defineComponent) {
        return defineComponent(ChangePassword);

        function ChangePassword() {
            this.after('initialize', function() {
                this.$node.html('Change Password');
            })
        }
    })
});
```

Remember to add a i18n value in a MessageBundle.properties. This will be displayed in the left pane

```text
useraccount.page.[Page Identifier].displayName=[String to display]
```

For example:

```text
useraccount.page.changePassword.displayName=Change Password
```

## User Account Setting Plugin

Plugin to add a general setting to the user account settings page

To register a boolean setting:

```javascript
require([
  'public/v1/api',
], function(bc) {
    bc.registry.registerExtension('org.bigconnect.user.account.page.setting', {
        identifier: 'my-bool-setting',
        group: 'useraccount.page.settings.setting.group.test',
        displayName: 'org.project.setting.myBoolean',
        type: 'boolean',
        uiPreferenceName: 'org.project.myBoolean'
    });
});
```

To register a custom control setting:

```javascript
require([
  'public/v1/api'
], function(bc) {
    bc.registry.registerExtension('org.bigconnect.user.account.page.setting', {
        identifier: 'my-custom-setting',
        group: 'useraccount.page.settings.setting.group.test',
        displayName: 'org.project.setting.myCustom',
        type: 'custom',
        componentPath: 'org/project/setting/MyCustom'
    });
});
```

