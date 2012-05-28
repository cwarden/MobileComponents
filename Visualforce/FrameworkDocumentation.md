# Core Framework Documentation #

## Javascript Documentation ##

**$V.App** - The core Javascript object utilized by the VF Mobile Components framework. An App consists of Pages, which itself consists of Components. The $V.App object, then, manages the showing/hiding of all Pages that have been registered with it. If a page has only one component, you only need to register the component, and the App will find the page and register it for you. A registered component is expected to render its UI to the DOM whenever it's page is requested by the app. This object, as well as registered components, are expected to dynamically request data from the server to generate the view. So far, jQuery Mobile is exclusively used to attach mobile-optimized style and behavior to the JS-generated HTML.

**$App Config** - A place to store any info that must be passed between the client-side and server-side parts of the framework.

- elemId - The id of the app's root element.
- debug - Specifies the debug level for things like logging and min scripts.
- jsCtlrName - The name of the Javascript controller for the component. The controller must extend $V.Component.
- fields - An array of Salesforce field metadata objects.
- serverCtlrName - The name of the Apex controller, which is used to talk to the server through Javascript remoting.
- user - An object that holds information about the current user.

*jQuery Mobile Event Listeners*

- pagebeforeload - Just logs the page load.
- pagebeforechange - Finds the cached Page that will be updated, and calls its rerender method.
- pagebeforecreate - Ensures that Page object is created or cached in the App object.
- pageinit - Finds the cached Page that the window is init-ing, and calls its render method.
- pagechange - Finds the cached Page that the window is changing, and calls its postrender method.
- pagechangefailed - Just logs the failure.

*JS Remoting Event Listeners*

- beforecall - Just logs the request.

**$App Methods**

- init - Attaches event listeners (listed above), and declares private variables/methods. Is executed immediately, on page load.
- registerPage - Creates the specified Page object and caches it in the App object.
- registerComponent - Finds the component handler function and caches it. Then finds the component's containing Page, ensures it is created/cached in the App object with the registerPage method, and attaches the component handler function to the Page.
- prepare - Moves class out of component-created wrapper, fetches user and fields metadata from server and stores on config.user and config.fields, and then calls the render method on each registered component's controller. Is executed on document ready. [REVIEWME: Why does 'prepare' render all components? Should it not do only data loading? Can we add a 'render' method that finds and renders only the first page?]
- getPage - Gets the cached page object that has the specified id.
- getFn - Finds the specified function on the specified context, using the window by default.
- toObject -
- getFieldVal - Gets the value of the specified field from the specified record object.
- getFieldValTemplate - Creates a template string compatible with jQuery template.
- handleRemoteErrs - Builds an HTML string for any errors on the specified result, and passes them to the $V.App.displayMsgs method. It then fires the 'error' trigger on the specified element.
- displayMsgs - _not yet implemented_ - Should display errors in the mobile UI.
- clearMsgs - _not yet implemented_ - Should remove any displayed errors in the mobile UI.


**$App.log**
- A standard means of logging for the Javascript parts of the SDK.

**$App.log Methods**

- info
- debug
- error

**$App.log Properties**

- prefix = 'Mobile VF: ' - A simple string to append to the $App.log methods.



**$V.Class** - A standard class that provides OOP-style inheritance in Javascript. It is used primarily by instances of the Component class to support the addition of new types of renderable components. The 'init' method is given the behavior of an OOP constructor, and access to a parent class' init is provided by calling this._super with any arguments. Class inheritance is performed by using the 'extend' method of any class that inherits from $V.Class, like this: 

        $V.ListComponent = $V.Component.extend({ /* Method definitions */});
        

**$V.Component** - _extends Class_ - All VF Mobile Components must extend this class. Inheriting classes must implement the 'init', 'prepare', and 'render' methods, and must call the this._super method before any implementation code.

**$V.Component Config**

- elemId - The id of the component's root element.

**$V.Component Properties**

- config - An object that keeps component state. It is passed between the JS controller and server controller and acts as component parameters.
- $me - A jQuery object for the component's root element.
- prepared - Indicates whether the component is prepared to be rendered.   - context -

**$V.Component Methods**

- init - Validates config property and initializes the $me property.
- prepare - Sets the component's 'prepared' property to true. Intended to be overridden by inheriting classes. If the inheriting class must modify the DOM or request data from the server before rendering, the code belongs in this method's inheritor. [REVIEWME: Consider renaming to beforeRender. However, there is also $V.App.prepare, which must be called on page load, in App.component.]
- render - Calls the prepare method. Intended to be overridden by inheriting classes. If an inheriting class intends to render itself onto the DOM, the rendering code belongs in this method's inheritor.
- getContext - Gets the value of the context property.
- parseField - Parses the specified field name, which might be fully qualified, and returns just the field name in the second position of an array.


## Apex Documentation ##

**BaseConfigController** - This class provides a config object for a config-based controller to use. This config object is passed back and forth between the JS controller and server controller for both sides to reference.

**BaseConfigController Properties**

- BaseConfig config

**BaseConfigController Methods**

- getConfigJson


**BaseConfig** - This class provides common client-side properties. Many config properties are not set in the server controller, but are set directly via component parameters.

**BaseConfig Properties**

- elemId - The id of the component's root element.
- jsCtlrName - The name of the Javascript controller for the component.

**BaseConfig Methods**

- toArray - Returns an array of values, split on commas, for the specified string. [REVIEWME: I don't think this method is ever used.]
- getFullyQualifiedClassName - Gets the name of the class for the specified type.


**AppConfig** - _extends BaseConfig_ - This class provides properties, additional to BaseConfig, for the App component and its Javascript controller to use.

**AppConfig Properties**

- debug - Indicates whether the component is in debug mode.
- serverCtlrName - The name of the server-side controller class.


**AppController** - _extends BaseConfigController_ - The AppController class is mainly used to generate a config object at the app level to pass back to the Javascript controller. This class also contains methods that specify whether to load full Javascript files or their minified versions, depending on the value of the debug property of the config object.

**AppController Properties**

**AppController Methods**

- getConfig - Gets the config property.
- getJqueryJsUrl - Gets the URL of the jQuery Javascript file.
- getJqueryTemplateJsUrl - Gets the URL of the jQuery Templates plugin.
- getJqueryMobileJs - Gets the name of the jQuery Mobile JS file.
- getMobileSplitViewJs - Gets the name of the jQuery Mobile split view JS file.
- getMobileSDKJs - Gets the name of the Visualforce Mobile Components JS file.
- getAdditionalScripts - Creates a Visualforce component with links to any additional JS files.

**AppController Remote Methods**

- getUser - Gets a map of information about the current user.
- getFieldMetadata - Gets metadata information about each specified field.


