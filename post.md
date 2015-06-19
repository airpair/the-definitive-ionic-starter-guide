[Ionic](http://ionicframework.com) is a powerful tool for building mobile apps using HTML, CSS, and JavaScript. This starter guide focuses on getting up and running with a realistic mobile app and learning about the primary features of Ionic. You will see many features of Ionic with a complete app example. You will end this guide with a strong command of Ionic and how it enables you to build beautiful, functional mobile apps.

Ionic is build on Angular and I will assume you are at least moderately familiar with Angular and the basics of web applications. If you need some help with Angular, I recommend reading up from Todd Motto's post [AngularJS Tutorial: A Comprehensive 10,000 Word Guide](https://www.airpair.com/angularjs/posts/angularjs-tutorial). It is also assumed you have NodeJS installed.

During this guide you will learn how to build a stock tracking app. You can [preview the completed app here](https://ionic-starter-guide.herokuapp.com/) and see the whole project on GitHub at [gnomeontherun/ionic-definitive-guide](https://github.com/gnomeontherun/ionic-definitive-guide). You can resize the browser to see how it would appear on mobile, or if you are using Chrome you can use the [device emulator feature](https://developer.chrome.com/devtools/docs/device-mode).

## Ionic, the missing hybrid app SDK

Ionic is built for [hybrid apps](http://java.dzone.com/articles/three-types-mobile-experiences), which are mobile apps that are written with HTML, CSS, and JavaScript instead of the native platform languages (Java for Android, Swift for iOS). When building a native app, you have access to the SDK which includes interface components such as [tabs](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/TabBarControllers.html) and [complex lists](http://developer.android.com/design/building-blocks/lists.html). These are the interface controls that you are familiar with from using mobile apps, and Ionic provides a comprehensive set of components for building hybrid apps.

However, Ionic is really more than just interface components. It also:

* provides a fantastic CLI utility for managing projects.
* leverages SASS to easily customize components.
* is built and maintained by a professional team, with a strong community behind it.
* incorporates a pluggable architecture for including 'ions' (additional components and features).
* has a large set of icons.

In addition, Ionic has a whole platform of services to support your apps, such as [Creator](http://docs.ionic.io/tools/creator/) for a visual drag/drop design experience, [View](http://docs.ionic.io/tools/viewapp/) for sharing a preview/beta version of your app with anyone, and [Push](http://docs.ionic.io/push/) for easily setting up push notifications. Recently added were [Deploy](http://docs.ionic.io/v1.0/docs/deploy-from-scratch) and [Analytics](http://docs.ionic.io/v1.0/docs/analytics-from-scratch) which are currently in alpha. You can expect Ionic to expand into a complete platform to serve the needs of app developers.

## Setup for Ionic

First things first, we need to get Ionic setup. For this tutorial we will be previewing in a browser, not on a mobile device (though you could if you follow the [Ionic guide](http://ionicframework.com/docs/guide/) for details on emulating and loading to a device). You need to have [Node](https://nodejs.org) installed on your system before you can install Ionic (Note: io.js may not work properly). Run this command in the terminal/command line prompt:

    $ npm install -g ionic

Here the Node Package Manager (NPM) will download and install the Ionic CLI. This is essential for building Ionic apps, and we'll use it to setup, preview, and build our app. Let's cover the primary types of features in Ionic.

## Ionic components & services

Before we jump into building our app, I wanted to give you a quick overview of Ionic's primary features. Ionic provides two primary features: components and services.

#### Ionic Components

Components are the user interface elements you declare using markup and CSS classes such as tabs, headers, slideshows, side menus, and more. These components either just CSS classes (like CSS frameworks like Bootstrap) or Angular directives. Some of the CSS components (`input`, `card`, `button`) don't provide additional features, but Ionic provides nicer styling that works well on mobile. The directive components (`sidemenu`, `list`, `slidebox`) are available as HTML tags.

```markup,linenums=true
<!-- Card: CSS Component Example -->
<div class="list card">
  <div class="item">Basic card!</div>
</div>
<!-- SlideBox: Directive Component Example -->
<ion-slide-box slide-interval="10000" does-continue="true">
  <ion-slide>Slide 1</ion-slide>
  <ion-slide>Slide 2</ion-slide>
</ion-slide-box>
```

In the two examples above, the first is a visual card like you might see in many apps like Google Now. It is created simply by using the CSS classes. The second is a slide box, which is a directive and declared using HTML tags. The slide box example could also include attributes, like `slide-interval` which provide configuration to the slide box (in this case sets the length of time for each slide to display).

#### Ionic Services

Services are programmatic user interface elements that are declared in JavaScript. These are typically declared in your controllers, and are interface elements that have a limited display (such as modals, popups, loaders). Just like Angular services, Ionic services all start with a `$` and are very clearly named like you see in this example.

```javascript,linenums=true
function Controller($scope, $ionicSlideBoxDelegate) {
  $scope.next = function() {
    $ionicSlideBoxDelegate.next();
  }
  $scope.previous = function() {
    $ionicSlideBoxDelegate.previous();
  }
}
```

In this controller, there are two scope methods that control the slide box using the service (any services that manage a component are delegate services). This would allow any custom button to change the slide, such as these two buttons.

```markup,linenums=true
<button ng-click="next()">Next</button>
<button ng-click="previous()">Previous</button>
```

Other services create a new visual experience, such as the loader. These will inject content into the current view as needed to create the desired effect, in this case a loading screen will overlay with a message, and after 2 seconds it will automatically hide.

```javascript,linenums=true
function Controller($timeout, $ionicLoading) {
  $ionicLoading.show({
    template: 'Loading'
  });
  $timeout(function() {
    $ionicLoading.hide();
  }, 2000);
}
```

Now let's get a new project started and see these things in action. If at any point you want to see the entire codebase, check out the [gnomeontherun/ionic-definitive-guide](https://github.com/gnomeontherun/ionic-definitive-guide) GitHub project.

## Starting the Ionic project

Let the fun begin! The first thing is to generate a new project. The Ionic CLI we installed before will help us do this.

    $ ionic start stocks https://github.com/ionic-in-action/starter
    $ cd stocks
    $ ionic serve

This will create a new blank app called `stocks` based on the starter app I created for my book [Ionic in Action](http://www.manning.com/wilken?a_aid=ionicinaction). This starter app is blank, and is ideal for new projects. The `ionic serve` command should have opened up the new app in your browser, and it will be just a blank page. Have no fear, we shall fix that now.

## Setting up Sass for styling

Ionic comes with a very helpful feature for customizing the default components and color presets using [Sass](http://sass-lang.com/). It is also recommended that you write any custom styles in the same way to take advantage of the variables and auto-generation provided by Ionic.

Sass support is not enabled by default in a new project. The following command will setup your project with Sass support.

    $ ionic setup sass

This command will generate a new CSS file based on the the scss/ionic.app.scss file, and output it to css/ionic.app.css. Then it will update the index.html file to load the new CSS file instead. Lastly, it enables Ionic commands to automatically regenerate the styles when you build the app, so you can't forget.

This app needs some styling, so you'll need to replace the contents of scss/ionic.app.scss file with the following.

```scss,linenums=true
// Override variables
$light: #eee;
$lighter: #fff;
$darker: #363636;
$assertive: #B33F33;
$balanced: #70AB23;
$positive: #366091;

// The path for our ionicons font files, relative to the built CSS in www/css
$ionicons-font-path: "../lib/ionic/fonts" !default;

// Include all of Ionic
@import "www/lib/ionic/scss/ionic";

// Dark theme for backgrounds.
.view, .pane, .modal {
  background: $darker;
}

// Style the form inside of the footer bar
.bar-search {
  padding: 0;

  form {
    display: block;
    width: 100%;
  }

  .item {
    padding: 3.75px 10px;
  }
}

// Make the item-dark style default by extending it back on top of item
.item {
  @extend .item-dark;
}

//
.item-reorder .button.icon {
  color: $light;
}

// Make inputs nicer on dark
.item-input {

  input {
    margin-right: 30px;
    padding-left: 5px;
    background: $darker;
    color: $light;
  }
  .input-label {
    color: $light;
  }
}
.item-input-wrapper {
  background: $darker;

  input {
    color: $lighter;
  }
}

// Make toggler nicer on dark
.toggle .track {
  background-color: $dark;
  border-color: $darker;
}
.toggle input:checked + .track {
  background-color: $positive;
  border-color: $positive;
}

// Remove a bottom border
.tabs-striped .tabs {
  border-bottom: 0;
}

// Allow the tabs to be as wide as needed
.tab-item {
  max-width: none;
}

// Styles for the quote component
.quote {
  background: $darker;
  padding: 5px;
  border-radius: 4px;
  display: block;
  position: absolute;
  top: 1px;
  right: 16px;
  text-align: center;
  width: 90px;
  height: 50px;
  color: $lighter;

  .spinner svg {
    margin-top: 6px;
  }

  &.positive {
    background: $balanced;
    color: $light;
  }

  &.negative {
    background: $assertive;
    color: $light;
  }

  .quote-change {
    font-size: 0.8em;
    color: $light;
  }
}
```

At the top of these styles, several of the Ionic variables are overridden with some custom colors (so it doesn't appear with the same colors as default Ionic). The rest of the styles are commented, and it will be easier to review all of the styles when the app is completed.

This will setup all of the styles for the app. It is best to start a project setting up Sass in your project at the start, though you can do it anytime. Next you will start getting the base navigation setup.

## Add Ionic's navigation components

Navigation is core to all apps, and Ionic provides several components that can be useful for navigation. In this example, you will use the `ionTabs` and `ionNavView` components to have tabs at the bottom to navigate between views. The `ionSideMenus` component is often used as well to expose a list of links for navigation.

Ionic is built using the popular ui-router project, which is an enhanced replacement for Angular's core ngRoute component. Ionic adds another layer of enhancements on top of ui-router, which is baked into the Ionic components and services. It leverages the idea of declaring states, which is a place in the app that describes the associated controller, view, template, and possibly other details. It will become more clear as we see some examples.

The `ionNavView` component is typically the center of an Ionic app's navigation, and works with several other components to allow you to craft intuitive navigation. Open up the www/index.html file, and update the body of the HTML file with the following.

```markup,linenums=true
<body ng-app="App">
  <!-- The ionNavBar updates the title and buttons as the application state changes -->
  <ion-nav-bar class="bar-positive">
    <!-- The ionNavBackButton knows when to show or hide based on current state -->
    <ion-nav-back-button></ion-nav-back-button>
  </ion-nav-bar>
  <!-- Primary ionNavView which will load the views -->
  <ion-nav-view></ion-nav-view>
</body>
```

Here you see three components, `ionNavView`, `ionNavBar`, and `ionNavBackButton`. The `ionNavBar` will contain our header content, such as the `ionNavBackButton` and a title of the state. Navbars are very common in apps, and it will automatically update the title as the user navigates between views, and conditionally show the `ionNavBackButton` when a user is allowed to go back in the history.

Now if you save these changes and look at the browser, you'll now see a blue navbar along the top. The `ionNavBar` is gray by default, but with the class `bar-positive` added it adopts a new color. There is a set of color presets that many components can adopt, and you'll see them sprinkled in throughout the example.

This app isn't very impressive yet, because we haven't defined any states to actually load (hence the blank screen). Let's get the first state setup.

## Add the tabs state

It is time to add the tabs state's template. You will declare the template which will contain the tabs component with two tabs for the `tabs.quotes` and `tabs.portfolio` states. You'll need to create a new file at www/views/tabs/tabs.html and add the following markup.

```markup,linnums=true
<!-- ionTabs wraps the ionTab directives -->
<ion-tabs class="tabs-icon-top tabs-dark tabs-striped">
  <!-- Quotes tab -->
  <ion-tab title="Quotes" icon-on="ion-ios-pulse-strong" icon-off="ion-ios-pulse" ui-sref="tabs.quotes">
    <!-- ionNavView for the quotes tab -->
    <ion-nav-view name="quotes"></ion-nav-view>
  </ion-tab>
  <!-- Portfolio tab -->
  <ion-tab title="Portfolio" icon-on="ion-ios-paper" icon-off="ion-ios-paper-outline" ui-sref="tabs.portfolio">
    <!-- ionNavView for the portfolio tab -->
    <ion-nav-view name="portfolio"></ion-nav-view>
  </ion-tab>
</ion-tabs>
```

Here the `ionTabs` component contains two `ionTab` components, which will display two tabs. The attributes declare icons for when the tab is active and inactive, as well as a link to a particular state using the `ui-sref` attribute. Inside of each `ionTab` are `ionNavView` components, which must have a name. You can only have one `ionNavView` that is unnamed, and that is in the index.html (which acts as the default). Later, when you create the tab states you will declare the states to match these specific `ionNavViews`. This is done so that each tab can have its own, independent navigation history, which will be demonstrated in depth later.

Now create www/views/tabs/tabs.js and add the following JavaScript that declares the 'tabs' state. Be sure to also add a script tag to the index.html file after the app.js file to load this file.

```javascript,linenums=true
angular.module('App')

.config(function($stateProvider) {
  $stateProvider
    .state('tabs', {
      abstract: true,
      url: '/tabs',
      templateUrl: 'views/tabs/tabs.html'
    });
});
```

The `tabs` view is `abstract`, which is unique to properly support nested `ionNavView` components like you see inside of the tabs. This simply means you will never go directly to just the `tabs` state, but in fact you will always go to a child state. Or in other words, it means you always go directly to one of the two tabs because it doesn't make sense to navigate to the tabs without one of them being selected.

Sadly, yet again, the screen is blank. Remember, the tabs view is `abstract` so you can only see it when you navigate to a child tab state. That means the next step is to create the first child tab state, so the tabs will appear and some very interesting things will start to happen.

## Add the services

But wait! You will need some Angular services to help manage data for this app, and I don't want to spend a lot of time on them. You can look through the comments to see how they work, and should be familiar to Angular developers. The first is a simple service to help manage data in localStorage, and the second is a service to load stock quotes from Yahoo! Finance.

First create the file www/js/localstorage.js with the content in the code below. Be sure to add a script tag to index.html.

```javascript,linenums=true
angular.module('App')

.factory('LocalStorageService', function() {

  // Helper methods to manage an array of data through localstorage
  return {
    // This pulls out an item from localstorage and tries to parse it as JSON strings
    get: function LocalStorageServiceGet(key, defaultValue) {
      var stored = localStorage.getItem(key);
      try {
        stored = angular.fromJson(stored);
      } catch(error) {
        stored = null;
      }
      if (defaultValue && stored === null) {
        stored = defaultValue;
      }
      return stored;
    },
    // This stores data into localstorage, but converts values to a JSON string first
    update: function LocalStorageServiceUpdate(key, value) {
      if (value) {
        localStorage.setItem(key, angular.toJson(value));
      }
    },
    // This will remove a key from localstorage
    clear: function LocalStorageServiceClear(key) {
      localStorage.removeItem(key);
    }
  };

});
```

Now create another file at www/js/quotes.js with the following code. Again, make sure to add a script tag to the index.html.

```javascript,linenums=true
angular.module('App')

.factory('QuotesService', function($q, $http) {

  // Create a quotes service to simplify how to load data from Yahoo Finance
  var QuotesService = {};

  QuotesService.get = function(symbols) {
    // Convert the symbols array into the format required for YQL
    symbols = symbols.map(function(symbol) {
      return "'" + symbol.toUpperCase() + "'";
    });
    // Create a new deferred object
    var defer = $q.defer();
    // Make the http request
    $http.get('https://query.yahooapis.com/v1/public/yql?q=select * from yahoo.finance.quotes where symbol in (' + symbols.join(',') + ')&format=json&env=http://datatables.org/alltables.env').success(function(quotes) {
      // The API is funny, if only one result is returned it is an object, multiple results are an array. This forces it to be an array for consistency
      if (quotes.query.count === 1) {
        quotes.query.results.quote = [quotes.query.results.quote];
      }
      // Resolve the promise with the data
      defer.resolve(quotes.query.results.quote);
    }).error(function(error) {
      // If an error occurs, reject the promise with the error
      defer.reject(error);
    });
    // Return the promise
    return defer.promise;
  };

  return QuotesService;
});
```

Ok, now it is time to create the quotes state, which leverages these two services.

## Add the quotes state

It's time to jump into high gear. The quotes tab is able to display a list of stocks that you wish to follow, show details about the current bids, allow you to lookup new quotes and add them, and even sort the list. This is an awful lot of functionality, but only requires about 30 lines of HTML and about 70 lines of JavaScript.

You'll first add all of the code for this state, and then we'll review how each part works individually. Create a new file at www/views/quotes/quotes.html and add the following markup.

```markup,linenums=true
<ion-view view-title="Market Quotes">
  <!-- Add a nav button to the primary location -->
  <ion-nav-buttons side="primary">
    <button class="button button-clear" ng-click="state.reorder = !state.reorder">Reorder</button>
  </ion-nav-buttons>
  <!-- ionContent is used to wrap scrollable content -->
  <ion-content>
    <!-- ionRefresher activates when the user pulls down and calls getQuotes() -->
    <ion-refresher on-refresh="getQuotes()"></ion-refresher>
    <!-- ionList allows us to make a complex list with swipe buttons, reordering, and more -->
    <ion-list show-reorder="state.reorder">
      <!-- Repeat over each quote, to display the current details and name -->
      <ion-item ng-repeat="stock in quotes" class="item-dark">
        <div class="quote" ng-class="quoteClass(stock)">
          <div class="quote-price">{{stock.LastTradePriceOnly | currency:'$'}}</div>
          <div class="quote-change">{{stock.Change}}</div>
        </div>
        {{stock.symbol}}
        <!-- Option and reorder button for list items -->
        <ion-option-button class="button-assertive" ng-click="remove($index)">Remove</ion-option-button>
        <ion-reorder-button class="ion-navicon" on-reorder="reorder(stock, $fromIndex, $toIndex)">
      </ion-item>
    </ion-list>
  </ion-content>
  <!-- Footer sits above the tabs and sticks -->
  <ion-footer-bar class="bar-search">
    <!-- Form to input a quote to add to the list -->
    <form ng-submit="add()" class="list">
      <div class="item item-input-inset">
          <label class="item-input-wrapper">
            <input type="search" placeholder="Symbol" ng-model="form.query">
          </label>
          <input type="submit" class="button button-small button-positive" value="Add" />
      </div>
    </form>
  </ion-footer-bar>
</ion-view>
```

Now create another file at www/views/quotes/quotes.js and add the following JavaScript. Also be sure to add the script tag to this file in the www/index.html file.

```javascript,linenums=true
angular.module('App')

.config(function($stateProvider) {
  // Declare the state for the quotes, with the template and controller
  $stateProvider
    .state('tabs.quotes', {
      url: '/quotes',
      views: {
        quotes: {
          controller: 'QuotesController',
          templateUrl: 'views/quotes/quotes.html'
        }
      }
    });
})

.controller('QuotesController', function($scope, $ionicPopup, $ionicLoading, LocalStorageService, QuotesService) {

  // Get symbols from localstorage, set default values
  var symbols = LocalStorageService.get('quotes', ['YHOO', 'AAPL', 'GOOG', 'MSFT', 'FB', 'TWTR']);
  $scope.form = {
    query: ''
  };
  $scope.state = {
    reorder: false
  };
  // Function to update the symbols in localstorage
  function updateSymbols() {
    var symbols = [];
    angular.forEach($scope.quotes, function(stock) {
      symbols.push(stock.Symbol);
    })
    LocalStorageService.update('quotes', symbols);
  }
  // Method to handle reordering of items in the list
  $scope.reorder = function(stock, $fromIndex, $toIndex) {
    $scope.quotes.splice($fromIndex, 1);
    $scope.quotes.splice($toIndex, 0, stock);
    updateSymbols();
  };
  // Method to load quotes, or show an alert on error, and finally close the loader
  $scope.getQuotes = function() {
    QuotesService.get(symbols).then(function(quotes) {
      $scope.quotes = quotes;
    }, function(error) {
      $ionicPopup.alert({
        template: 'Could not load quotes right now. Please try again later.'
      });
    }).finally(function() {
      $ionicLoading.hide();
      $scope.$broadcast('scroll.refreshComplete');
    });
  };
  // Method to load a quote's data and add it to the list, or show alert for not found
  $scope.add = function() {
    if ($scope.form.query) {
      QuotesService.get([$scope.form.query]).then(function(results) {
        if (results[0].Name) {
          $scope.quotes.push(results[0]);
          $scope.form.query = '';
          updateSymbols();
        } else {
          $ionicPopup.alert({
            title: 'Could not locate symbol.'
          });
        }
      });
    }
  };
  // Method to remove a quote from the list
  $scope.remove = function($index) {
    $scope.quotes.splice($index, 1);
    updateSymbols();
  };
  // Method to give a class based on the quote price vs closing
  $scope.quoteClass = function(quote) {
    if (quote.PreviousClose < quote.LastTradePriceOnly) {
      return 'positive';
    }
    if (quote.PreviousClose > quote.LastTradePriceOnly) {
      return 'negative';
    }
    return '';
  };
  // Start by showing the loader the first time, and request the quotes
  $ionicLoading.show();
  $scope.getQuotes();

});
```

At this point, if `ionic serve` is still running your app should be loaded in the browser. Go to http://localhost:8100/#/tabs/quotes and this new view will appear with the list of current stock quotes.

If you look at the controller, you will see that there are a number of methods that you might see in any Angular app, such as the `quoteClass()` method which determines if the quote box should be green or red or `getQuotes()` which handles the loading of data. Several methods will be called by Ionic components, such as `remove()` and `reorder()` by the Ionic List component.

Now let's take a look at the Ionic components and services that make this view work.

#### Ionic View, Nav Buttons, and Content Components

The foundation of our view is the `ionView` component, which is used to wrap a template. The `view-title` attribute allows the view to set the title in the `ionNavBar`, and other attributes are available to control other aspects of the navbar (such as if the view should be cached or if it should show the back button). Every view will have the `ionView` component wrapping the rest of the content.

Inside of the `ionView` is the `ionNavButtons` component. This will add buttons to the navbar when this view is active, and you are able to declare which side they appear. In this example, it uses `side="primary"` which allows the buttons to be placed on the primary location for buttons based on the platform. In iOS that is on the left, and for Android that is right, and demonstrates how Ionic has many features that are 'platform aware'. This button uses the `ngClick` directive to run an expression, which will be covered in the list component.

Then you see the `ionContent` component. Think of this as the content wrapper, and it has a number of benefits.

1. It sizes the content area to the available space.
2. It automatically ensures content will scroll vertically if it is taller than the viewport.
3. It is aware of other components, such as the navbar, tabs, and footer, and will resize to allow room for those components.
4. It allows the Ionic Refresher component to work seemlessly with your content.
5. It is very configurable, and allows you to set values for features such as padding, scrolling direction, scroll locking.

You will use `ionContent` to wrap your content in most of your views, and most of the time with only the default settings.

These several components work together to provide the primary container structure for our view, and you'll see them again in the next view since they are quite common.

#### Ionic Loading Service

The Ionic Loading service overlays the screen with a configurable loading message until the content has loaded, so it prevents the user from interacting with the view until its ready. From a usability perspective, this should be reserved for when the interface can only work once data has loaded. In this example, it only will appear the first time the app has to load the data.

When the controller is first loaded, the `$ionicLoading.show()` method is called. This triggers the loading indicator to appear, and it will remain until the `$ionicLoading.hide()` method is called. Right after the `show()` method, the controller calls `$scope.getQuotes()` which handles loading the data. You can see the `hide() ` is called in the `finally()` promise callback to hide the loader after the data request has finished.

You might think the loader could auto hide when the data has loaded, but the loader requires you to call the `hide()` method because it cannot know exactly when everything is ready for the view. The loader is also like a singleton, meaning there is only ever one loader on the screen. If you call `show()` multiple times, it will still only take one call to `hide()` to hide it.

#### Ionic Refresher Component

The Ionic Refresher is a component you put inside of a view that allows the user to pull down to refresh the view. As the user pulls down, an icon slides down and if they pull far enough and let go it triggers the refresh. You've likely seen this in many apps that allow you to reload the data, such as an app showing sports scores or (like this example) stock quotes. It is a common pattern that uses the pull down gesture instead of a button.

The component is just a directive called `ionRefresher`, and has a single attribute `on-refresh` that accepts an expression to evaluate when the user releases the pull. In this case, it calls the `getQuotes()` method, which reloads the data and update the view. Similarly to the Loading service, we use the `finally()` callback to broadcast an event `$scope.$broadcast('scroll.refreshComplete');` that will tell the Refresher the reload is complete and to hide itself.

The primary key to making the Refresher component work is to ensure that you have a single scope method that (re)loads the data in the view. Since the Refresher is a component and not a service (like the Loader), it listens for the `scroll.refreshComplete` event to know when to hide. If you forget to call this, the Refresher will display forever.

#### Ionic Popup Service

The Ionic Popup service is used to display an overlay for the user to acknowledge or interact with. It will block the current view and overlay a box with a message and button. There are several built in types of popups and you can also build your own.

* Alert: Shows a simple message and button to clear. Useful to inform users of something such as an error.
* Confirm: Shows a simple message and two buttons to either confirm or cancel. Useful to confirm an action such as deleting an item.
* Prompt: Shows a simple message, an input field, and an OK and Cancel button. Useful when you need additional details, such as a password.

The Popup service is `$ionicPopup` and when you create one of the types of a popup it is returned as a promise. When you create a new popup, it will appear and you use the promise API to handle the callback when a button is selected. It is also possible to programmatically close the popup using the `close()` method.

In this view, the Popup service is used to help alert to errors in loading data. In the `getQuotes()` method, if the request promise is resolved with an error it will alert that it could not load the quotes. Likewise, when the user tries to add a quote that doesn't exist, it will alert that the symbol was not found. In this example you are not waiting for the popup promise to be resolved (with the alert is closed), it simply just closes and allows the user to continue using the app.

Popups can be easily abused from a usability standpoint. Much like an alert/confirm window you see in JavaScript, it blocks the interface and requires the user to interact to continue. Using Popups should be limited to situations when the user needs to acknowledge or respond to the Popup to continue. You could argue that this example could use a different method to give feedback to the user.

#### Ionic List Component

The Ionic List component is a powerful user interface component, which is really a combination of several different directives that work together. Lists are a very common and clean way to display content, as mobile content has limited space. Android and iOS have introduced many features to lists that users have become accustomed to using, such as the ability to reorder items, swiping to show additional buttons, and deleting items. So much of the data shown in apps are really just lists, such as lists of news articles, emails, locations visited, and more.

In this app, the List component supports reordering and deleting of items. The `ionList` component is used to wrap the `ionItem` components. The `ionItem`s have an `ngRepeat` so it will display the entire list of quotes that are stored (or the default list from the localStorage service). Then, each `ionItem` displays the quote and price.

The `ionList` has several attributes that control when the list is in a 'reorder' mode, which is a boolean that is evaluated. The `ionItem` contains an `ionReorderButton` to power this feature. When the `state.reorder` model is true, the reorder button (which is a stacked three lines icon) will slide into view and allow you to tap and drag the list items. When the item is released, it will call the `reorder()` method that was declared by the `on-reorder` attribute. It passes three items, the item, the index value of the item originally, and the new index after it was released. The `reorder()` method takes these values and moves the item in the `$scope.quotes` array.

The last feature is the `ionOptionButton`, which is a button that appears when the user swipes left on an item. This option button is designed to act like a delete button, so if the user taps on the button it will call the `remove()` method to remove that item from the list. You could make buttons for other purposes, such as to edit or share an item.

Ionic Lists are quite powerful, and very intuitive for users to manage lists. They are used so often, I would even suggest you can't use it too often. Anything that is a collection of items is probably ideal for a list. Ionic Lists can also be styled in many ways with default Ionic CSS classes, found in the documentation. Some common styles include lists with an avatar or icon to the side, large images like cover art, or cards style that modifies the entire list to have a similar card feel like you see in Google Now or Tinder style apps.

#### Ionic Footer Bar and Form

The Ionic Footer Bar is a way to display text or simple content at the bottom of the view. It automatically positions based on the other components, was discussed earlier with `ionContent`. It does need to be outside of the `ionContent` to position correctly, just like the `ionNavButtons`.

The Footer Bar in this app also has a simple form, which has some Ionic styling (plus a little extra custom styling in the Sass file). Form styles are not fully designed to work in the Footer Bar, but it only took a few lines of styling to clean it up. This form is just a simple search input and button, and on submit the form will call the `add()` method to attempt to lookup and add a stock symbol to the current list.

## Add the portfolio state

The last major step for your app will be to add another view to keep track of your portfolio. The idea is you can specify how many stocks you purchased at a given price, and see a current rate of return or loss for that purchase. This whole state has about 60 lines of markup and 100 lines of JavaScript.

Create a new file at www/views/portfolio/portfolio.html and add the contents below.

```markup,linenums=true
<ion-view view-title="My Portfolio">
  <!-- Add buttons to both primary and secondary sides of the navbar -->
  <ion-nav-buttons side="primary">
    <button class="button button-clear" ng-click="openModal()">Add</button>
  </ion-nav-buttons>
  <ion-nav-buttons side="secondary">
    <button class="button button-clear" ng-click="state.remove = !state.remove">Edit</button>
  </ion-nav-buttons>
  <ion-content>
    <!-- Use a list to display the list of stocks in portfolio -->
    <ion-list show-delete="state.remove">
      <!-- Repeat over each stock in the portfolio and display relevant data -->
      <ion-item ng-repeat="stock in portfolio" class="item-dark item-text-wrap">
        {{stock.symbol}} ({{stock.quantity}} @ {{stock.price | currency}})
        <div class="quote" ng-class="quoteClass(stock)" ng-if="quotes[stock.symbol]">
          <!-- These bindings calculate the current value and gain/loss of the stock -->
          <div class="quote-price">{{getCurrentValue(stock) | currency:'$'}}</div>
          <div class="quote-change">{{getChange(stock) | currency}}</div>
        </div>
        <!-- Use an ionSpinner to display while the stock price is loaded -->
        <div class="quote" ng-if="!quotes[stock.symbol]">
          <ion-spinner icon="lines"></ion-spinner>
        </div>
        <!-- Delete button allows the item to be removed -->
        <ion-delete-button class="ion-minus-circled" ng-click="remove($index)"></ion-delete-button>
      </ion-item>
    </ion-list>
  </ion-content>
</ion-view>
```

Create another file at www/views/portfolio/add-modal.html and add the following markup.

```markup,linenums=true
<!-- Modals require a special ionModalView wrapper -->
<ion-modal-view>
  <!-- Use Angular form to have automatic validation -->
  <form name="addQuote">
    <!-- Add an ionHeaderBar with buttons and title -->
    <ion-header-bar class="bar-balanced">
      <button class="button button-clear" ng-click="closeModal()">Cancel</button>
      <h1 class="title">Add Stock</h1>
      <button class="button button-clear" ng-click="addStock(item)" ng-disabled="addQuote.$invalid">Save</button>
    </ion-header-bar>
    <ion-content>
      <!-- Use Ionic's CSS list and form classes to format forms -->
      <div class="list">
        <label class="item item-input">
          <span class="input-label">Symbol</span>
          <input type="text" autocorrect="off" autocapitalize="off" ng-model="item.symbol" required />
        </label>
        <label class="item item-input">
          <span class="input-label">Quantity</span>
          <input type="number" autocorrect="off" autocapitalize="off" ng-model="item.quantity" required />
        </label>
        <label class="item item-input">
          <span class="input-label">Price</span>
          <input type="number" autocorrect="off" autocapitalize="off" ng-model="item.price" required />
        </label>
      </div>
    </ion-content>
  </form>
</ion-modal-view>
```

Lastly, create a file at www/views/portfolio/portfolio.js and include the code below. Also be sure to create a new script tag to load this into the app in www/index.html.

```javascript,linenums=true
angular.module('App')

.config(function($stateProvider) {
  // Declare the state for the portfolio, with the template and controller
  $stateProvider
    .state('tabs.portfolio', {
      url: '/portfolio',
      views: {
        portfolio: {
          controller: 'PortfolioController',
          templateUrl: 'views/portfolio/portfolio.html'
        }
      }
    });
})

.controller('PortfolioController', function($scope, $ionicModal, $ionicPopup, LocalStorageService, QuotesService) {
  // Create the portfolio model from localstorage, and other models
  $scope.portfolio = LocalStorageService.get('portfolio', []);
  $scope.item = {};
  $scope.state = {
    remove: false
  };
  // Private method to update the portfolio
  function updatePortfolio() {
    LocalStorageService.update('portfolio', $scope.portfolio);
  }
  // Method to get the latest quotes
  $scope.getQuotes = function() {
    var symbols = [];
    angular.forEach($scope.portfolio, function(stock) {
      if (symbols.indexOf(stock.symbol) < 0) {
        symbols.push(stock.symbol);
      }
    });

    if (symbols.length) {
      QuotesService.get(symbols).then(function(quotes) {
        var items = {};
        angular.forEach(quotes, function(quote) {
          items[quote.Symbol] = quote;
        });
        $scope.quotes = items;
      });
    }
  };
  // Method to calculate the current value of the stocks
  $scope.getCurrentValue = function(stock) {
    return parseFloat($scope.quotes[stock.symbol].LastTradePriceOnly) * stock.quantity;
  };
  // Method to calculate the change in value
  $scope.getChange = function(stock) {
    return $scope.getCurrentValue(stock) - stock.price * stock.quantity;
  };
  // Method to determine if the stock has positive or negative return for background color
  $scope.quoteClass = function(stock) {
    if (!stock) {
      return '';
    }
    var className = '';
    var currentValue = $scope.getCurrentValue(stock);
    if (currentValue && currentValue > stock.price) {
      className = 'positive';
    } else if (currentValue && currentValue < stock.price) {
      className = 'negative';
    }

    return className;
  }
  // Create an Ionic modal instance for adding a new stock
  $ionicModal.fromTemplateUrl('views/portfolio/add-modal.html', {
    scope: $scope
  }).then(function(modal) {
    $scope.modal = modal;
  });
  // Open the modal
  $scope.openModal = function() {
    $scope.modal.show();
  };
  // Close the modal and reset the model
  $scope.closeModal = function() {
    $scope.item = {};
    $scope.modal.hide();
  };
  // Ensure the modal is completely destroyed after the scope is destroyed
  $scope.$on('$destroy', function() {
    $scope.modal.remove();
  });
  // Method to add a new stock purchase
  $scope.addStock = function(item) {
    $scope.state.remove = false;
    QuotesService.get([item.symbol]).then(function(quote) {
      if (quote[0].Name) {
        item.symbol = item.symbol.toUpperCase();
        $scope.portfolio.push(item);
        updatePortfolio();
        $scope.closeModal();
        $scope.getQuotes();
      } else {
        $ionicPopup.alert({
          title: 'Could not find symbol.'
        });
      }
    });
  };
  // Method to remove an item from the portfolio
  $scope.remove = function($index) {
    $scope.portfolio.splice($index, 1);
    updatePortfolio();
  };
  // Get the quotes on load
  $scope.getQuotes();

});
```

Once all of these changes are made, ensure `ionic serve` is still running and go to http://localhost:8100/#/tabs/portfolio to see this new tab. You could also tap on the Portfolio icon in the tabs to navigate.

There are a few features of Ionic used here again, such as the Ionic Popup. However, in addition there are some new features such as the Ionic Modal, Ionic Header Bar, Ionic Spinner, and another feature for the Ionic List component.

#### Ionic Modal Service

The Ionic Modal service is a way to overlay an entire view on the app, and can typically be thought of as a temporary state. They are used to provide related, contextual content without cluttering the original view. In traditional desktop websites, modals have gotten a bad rap from usability professionals, but in a mobile context they are often more functional and user friendly. A modal is a completely new view, so it can contain anything from a form, to a scrolling list of content, to a video.

In this example, the `$ionicModal` service creates a modal based on a loaded template, which points to the www/views/portfolio/add-modal.html file (you can also use `fromTemplate()` instead to pass a template as a string, but I don't recommend it). The modal is created and loaded when the controller first executes, but it does not immediately open. Here is the section that creates a new modal.

```javascript
$ionicModal.fromTemplateUrl('views/portfolio/add-modal.html', {
  scope: $scope
}).then(function(modal) {
  $scope.modal = modal;
});
```

The `$ionicModal` service generates the modal, which is very similar to registering a new state except in this case the modal is not part of the app routing. You also pass an object with some configuration, and in this case you have `{scope: $scope}` which will use the current state's scope as the modal's scope. Creating a modal from a template returns a promise, and when it resolves the loading of the template it returns a modal instance which can be used to control the modal (such as open and close).

The app then has an `openModal()` and `closeModal()` which handle the opening and closing of the modal. The portfolio view contains a navbar button with `ngClick` that calls `openModal()`, and inside of the modal view there is a cancel button that calls `closeModal()`. If a user submits the form to add a stock quote, if the stock is found it will also call the `closeModal()` method to hide the modal and return to the portfolio.

Ionic Modals also require that you also add a listener for the scope `destroy` event, so you can manually remove the modal as well. Since modals are created manually and not registered with the state provider, Ionic doesn't know when they are safe to be deleted from memory. This is easy to do, as you see here.

```javascript
$scope.$on('$destroy', function() {
  $scope.modal.remove();
});
```

I find the Ionic Modal to be a very useful tool given the size of most mobile devices. Instead of trying to show lots of content on one long scrolling screen, the modals are able to provide one way to break that content up and show it only when it is relevant to the user action. If the add form was in the portfolio view instead of a modal, it would clutter the view, so the modal provides a better experience to only show it when the user is about to add a new item. They do take some design consideration since the modal will show until it is dismissed. It is also not possible to directly navigate to a modal (at least not without some additional custom logic to handle this), so anything that a user might directly navigate to is probably not a candidate for a modal.

#### Ionic Header Bar Component

The Ionic Header Bar is essentially the same as the Footer Bar, except it sits in the header as you might expect. There are several different color presets that can be used as CSS classes, and in this case it uses the `bar-balanced` class, which is modified from the default by the app Sass stylings. Since the modal is a blank slate that overlays the app, the navbar does not appear and requires you to add a Header Bar to place buttons along the top.

#### Ionic Spinner Component

The Ionic Spinner is a helpful component to display an animated SVG to indicate your app is doing something that the user has to wait for to complete. With animated SVG the indicators are be more flexible than with just rotating an icon with CSS. These spinners (which not all spin, so it is a bit of a misnomer) are possible to style and Ionic comes with 10 different animations.

In this app, the `lines` animation is used in place of the quote price while the quotes are loaded. The `ngIf` controls when to show the spinner.

#### Ionic List Component Continued

The Ionic List component also has a delete mode, which is similar to the reorder mode you saw in the quotes state but instead of moving items it will delete them. You can use both delete and reorder modes on the same list, depending on your app design and needs. The delete mode slides an icon from the left to allow users to quickly delete items.

To toggle the delete mode, the `ionList` has a `show-delete` attribute that should be `true` or `false` to toggle the delete mode. Then each of the `ionItem` components have an `ionDeleteButton` component, which have an icon declared and use an `ngClick` to call the `remove()` method in the controller.

## Finishing touches

There is one last thing that our app needs, a default route. Right now if you go to http://localhost:8100/ it will not load the tabs and content. This is an easy remedy, but needed to be implemented after the states were added. The following snippet should be placed into your www/js/app.js file, and it uses the `$urlRouterProvider.otherwise()` method to define where the app should go when it can't find a route (in this case the default `/` route is not registered).

```javascript,linenums=true
angular.module('App', ['ionic'])

.config(function($urlRouterProvider) {
  $urlRouterProvider.otherwise('/tabs/quotes');
})
```

Now when you go to the app without any path in the URL, it will load up the quotes view by default for you. Congratulations, you now have a fairly feature rich mobile app, with around 120 lines of HTML and 200 lines of JavaScript! Who knew you could build so many features so easily?

## Digging deeper into Ionic

We've flown through the example app, and learned a lot about how Ionic components work together to create a beautiful and functional mobile app using just HTML, CSS, and JavaScript. You've seen how to use many components and services such as Ionic Lists, Popups, Modals, Navigation, Content, Refresher, and others. This foundation should be great for you to build from to learn the rest of the components and dive deeper into other ways they can be leveraged.

The [Ionic documentation](http://ionicframework.com/docs) is really good and the best place to look up detailed information about how components and services work. It also provides some primers on how to get your environment setup to build and deploy the app to an emulator or to a real device. For that, you will be using [Cordova](http://cordova.apache.org/) and will want to spend some time understanding how it works. You can also spend time on the [Ionic Forum](http://forum.ionicframework.com/) to find answers or ask your own questions.

If you are looking for more, I recommend you check out my book [Ionic in Action](http://www.manning.com/wilken?a_aid=ionicinaction) which covers everything in much more detail. It also provides an Angular primer, lots of examples on how to use Ionic and Cordova plugins (with ngCordova), how to test, and how to properly build and submit to the app stores.
