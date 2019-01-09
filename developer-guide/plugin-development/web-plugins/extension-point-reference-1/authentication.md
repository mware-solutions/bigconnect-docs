---
description: Create custom login authentication mechanisms
---

# Authentication

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-authentication)

Create custom login authentication mechanisms.

![](../../../../.gitbook/assets/image%20%2810%29.png)

## Create a web plugin

The web plugin registers the resources needed, and creates a route the form will POST credentials. 

```java
app.registerBeforeAuthenticationJavaScript("/com/mware/examples/authentication/plugin.js");
app.registerJavaScript("/com/mware/examples/authentication/authentication.js", false);
app.registerJavaScriptTemplate("/com/mware/examples/authentication/login.hbs");
app.registerCss("/com/mware/examples/authentication/login.css");
app.registerResourceBundle("/com/mware/examples/authentication/messages.properties");

app.post(AuthenticationHandler.LOGIN_PATH, login);
```

[ExampleAuthenticationPlugin.java \(lines 24–30\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-authentication/src/main/java/com/mware/examples/authentication/ExampleAuthenticationPlugin.java#L24-L30)

This extension deviates from others in that the authentication `plugin.js` is registered using `registerBeforeAuthenticationJavaScript`. Since all plugin JavaScript isn't loaded until after login, we need a different way to add scripts to the page earlier. Only the plugin file that registers the extension needs to be registered in this way. The actual authentication component is registered using `registerJavaScript` with the second parameter, `includeInPage` set to false, resulting in the component not being loaded on page load, but is always available to RequireJS.

## Register Extension

Register the authentication extension in the `plugin.js` file:

```javascript
define(['public/v1/api'], function(bc) {
    'use strict';

    bc.registry.registerExtension('org.bigconnect.authentication', {
        componentPath: 'com/mware/examples/authentication/authentication'
    })
});
```

[plugin.js](https://github.com/mware-solutions/doc-examples/blob/master/extension-authentication/src/main/resources/com/mware/examples/authentication/plugin.js)

## Create the JS Component

Create the FlightJS authentication component. 

```javascript
define([
    'public/v1/api',
    './login.hbs'
], function(
    bc,
    template) {
    'use strict';

    return bc.defineComponent(ExampleAuthentication);

    function ExampleAuthentication() {

        this.defaultAttrs({
            errorSelector: '.text-error',
            usernameSelector: '.login input.username',
            passwordSelector: 'input.password',
            loginButtonSelector: '.login .btn-primary',
            signInButtonSelector: '.signin',
            loginFormSelector: '.login'
        });

        this.after('initialize', function() {
            var self = this;

            this.$node.html(template(this.attr));
            this.enableButton(false);

            this.on('click', {
                loginButtonSelector: this.onLoginButton,
                signInButtonSelector: this.onSignInButton
            });

            this.on('keyup change paste', {
                usernameSelector: this.onUsernameChange,
                passwordSelector: this.onPasswordChange
            });

            this.select('usernameSelector').focus();
        });

        this.onSignInButton = function(event) {
            event.preventDefault();

            var form = this.select('loginFormSelector').show();
            _.defer(function() {
                form.find('input').eq(0).focus();
            });
        };

        this.checkValid = function() {
            var self = this,
                user = this.select('usernameSelector'),
                pass = this.select('passwordSelector');

            _.defer(function() {
                self.enableButton(
                    $.trim(user.val()).length > 0 &&
                    $.trim(pass.val()).length > 0
                );
            });
        };

        this.onUsernameChange = function(event) {
            this.checkValid();
        };

        this.onPasswordChange = function(event) {
            this.checkValid();
        };

        this.onLoginButton = function(event) {
            var self = this,
                $error = this.select('errorSelector'),
                $username = this.select('usernameSelector'),
                $password = this.select('passwordSelector');

            event.preventDefault();
            event.stopPropagation();
            event.target.blur();

            if (this.submitting) {
                return;
            }

            this.enableButton(false, true);
            this.submitting = true;
            $error.empty();

            $.post('login', {
                username: $username.val(),
                password: $password.val()
            }).fail(function(xhr, status, error) {
                self.submitting = false;
                if (xhr.status === 403) {
                    error = i18n('com.mware.examples.authentication.invalid');
                }
                $error.text(error);
                self.enableButton(true);
            })
            .done(function() {
                self.trigger('loginSuccess');
            })
        };

        this.enableButton = function(enable, loading) {
            if (this.submitting) return;
            var button = this.select('loginButtonSelector');

            if (enable) {
                button.removeClass('loading').removeAttr('disabled');
            } else {
                button.toggleClass('loading', !!loading)
                    .attr('disabled', true);
            }
        };
    }
});
```

[authentication.js](https://github.com/mware-solutions/doc-examples/blob/master/extension-authentication/src/main/resources/com/mware/examples/authentication/authentication.js)

When the login request succeeds, the component triggers `loginSuccess`, this notifies BigConnect that the application loading process should attempt to continue loading. If the session is not valid, the front-end state is undefined.

```javascript
$.post('login', {
    username: $username.val(),
    password: $password.val()
}).fail(function(xhr, status, error) {
    self.submitting = false;
    if (xhr.status === 403) {
        error = i18n('com.mware.examples.authentication.invalid');
    }
    $error.text(error);
    self.enableButton(true);
})
.done(function() {
    self.trigger('loginSuccess');
})
```

[authentication.js \(lines 89–102\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-authentication/src/main/resources/com/mware/examples/authentication/authentication.js#L89-L102)

## Login Route

The login route uses BigConnect's `UserRepository` to create users, then prepares the session using `CurrentUser.set`.

```java
public JSONObject handle(
        HttpServletRequest request,
        @Required(name = "username") String username,
        @Required(name = "password") String password
) throws Exception {
    username = username.trim();
    password = password.trim();

    if (isValid(username, password)) {
        User user = findOrCreateUser(username);
        userRepository.updateUser(user, new UserNameAuthorizationContext(username, RemoteAddressUtil.getClientIpAddr(request)));
        CurrentUser.set(request, user.getUserId(), user.getUsername());
        JSONObject json = new JSONObject();
        json.put("status", "OK");
        return json;
    } else {
        throw new BcAccessDeniedException("", null, null);
    }
}
```

[Login.java \(lines 27–45\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-authentication/src/main/java/com/mware/examples/authentication/Login.java#L27-L45)

