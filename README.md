# Backbone.Transceiver
This is a Backbone plugin that simplifies the use of Backbone.Radio in Marionette applications. It allows to declaratively configure events, requests, and commands using a simple syntax:

```{javascript}
var MyApplication = Marionette.Application.extend({

    channels: {
        // configure app channel
        app: {
            reply: {
                'users': 'users'
                'users:current': 'getCurrentUser'
            },
            comply: {
                'show:modal': 'showModal'
            },
            events: {
                'change:lang': 'onLanguageChanged'
            },
            request: {
                'language': 'lang'
            }
        }
    },
    
    initialize: function() {
        // initialize
        this.users = new Users();
    },
    
    getCurrentUser: function() {
        return this.users.getCurrent();
    },
    
    showModal: function() {
        // show modal view using language requested from app channel
        var view = new ModalView(this.lang);
    },
    
    onLanguageChanged: function(language) {
        // handle changed language
    }

});
```

## Convenience access to channels
For each channel name that is used in the ```channels``` configuration object, a convenience property is added:

```{javascript}
var MyApplication = Marionette.Application.extend({

    channels: {
        app: {
            // ...
        },
        other: {}
    },
    
    notifyOthers: function() {
        this.channels.appChannel.trigger('change:something');
    }

});
```

If you only want access to a channel without configuring anything else, simply use an empty object as value.

## Replying to requests
With ```reply```, you can easily reply to requests from other objects in your application. The key corresponds to the request's name and the value is always a string corresponding to a property or function of the context object.

```{javascript}
var MyApplication = Marionette.Application.extend({

    channels: {
        // configure app channel
        app: {
            reply: {
                'users': 'users'
                'users:current': 'getCurrentUser'
            },
            // ...
        }
    },
    
    initialize: function() {
        // initialize
        this.users = new Users();
    },
    
    getCurrentUser: function() {
        return this.users.getCurrent();
    },
    
    // ...

});
```

## Complying to commands
With ```comply```, you can easily implement commands, which works basically the same as with replies, though only method names are allowed as values.

## Listening to events
```events``` allows you to handle any event that is triggered on a channel. Simply use the event handlers name as value.

```{javascript}
var MyApplication = Marionette.Application.extend({

    channels: {
        // configure app channel
        app: {
            // ...
            events: {
                'change:lang': 'onLanguageChanged'
            },
            // ...
        }
    },
    
    // ...
    
    onLanguageChanged: function(language) {
        // handle changed language
    }

});
```
## Requesting data
In addition to replying to requests from other entities, you can, of course, also easily request data. When requesting data for a certain key, a EcmaScript 5.1 getter is added to the context object, which will dynamically request the data when the corresponding property is accessed:

```{javascript}
var MyApplication = Marionette.Application.extend({

    channels: {
        // configure app channel
        app: {
            //...
            request: {
                'language': 'lang'
            }
        }
    },
    
    // ...
    
    showModal: function() {
        // accessing this.lang will dynamically request 'language' from the channel
        var view = new ModalView(this.lang);
    }
    
    // ...
    
});
```

# Limitations
Currently, it is not possible to use any of the dynamically configured features in ```initialize``` functions, as there is no easy way to hook into construction of a Backbone/Marionette object before ```initialize``` is called (see http://ianstormtaylor.com/backbonejs-configuration-logic-should-be-extendable/ for a description of the problem).
