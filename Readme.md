# IOWMVC

This is a a guide on how to use the following set of modules together to make
frontend JS applications:

- [ventnor views][ventnor]
- [merstone models][merstone]
- [chale collections][chale]

This guide is primarily a progression from using Backbone but will hopefully provide some insight regardless of your varying MVC framework mileage.

## TOC

- [Philosophy](#philosophy)
- [Structuring an application](#structuring-an-application)
  - [Active record vs. Service Oriented](#active-record-vs-service-oriented)
  - [The benefits of services](#the-benefits-of-services)

## Philosophy

These modules were born from an admiration of Backbone (and the proliferation
of other JS frameworks) that brought decent application structure and useful
bases for frontend development to the browser wilderness.

Generally the existing solutions suffer from bloatedness and brutal APIs. Lots
of frameworks enforce their own inheritance and object creation, but these
constructs already exist in JS. As soon as you built on top of something thats not
the common denominator (which is plain JS) you become incompatible with lots of
modules, or have to "frameworkify" things if you want to be compatible with them.

IOWMVC is about setting out some base classes that do the basic model view and
collection functionality, that then allow you to write your application logic
in plain ol' JS. So far, nothing else I've seen does this.

Inspired (but frustrated) by these monoliths, [ventnor][ventnor], [merstone][merstone]
and [chale][chale] – known collectively here as IOWMVC: **I**sle **o**f **W**ight
**MVC** – are a set of loosely coupled, modular components, subscribing heavily
to the "do-one-thing-well" approach.

Each of mentioned modules have a small enough surface area such that their source
code and documentation can be easily browsed and consumed. You should familiarise
yourself with each.

## Structuring an application

Given the modularity and composability of many small modules, structuring an
application leaves a lot up to the developer. In practice, however, it's useful
to have some pointers and best practices. If you know better, feel free to deviate,
otherwise heed the advice given here.

### Active record vs. Service Oriented

In an active record architecture (such as Backbone) the models and collections deal with their own persistence. This is an immediate violation of the "do-one-thing-well" policy, because not on top of being data encapsulations and an ordered set of models, they also have to know how to do persistence over HTTP APIs.

```js
// Active record

var model = new Model({ _id: '123' })
model.fetch({ success: function () { console.log('fetched!') } })
model.set('a', 10)
model.save({ success: function () { console.log('saved!') } })
```

In a service oriented approach (which if isn't clear already, is the preferred approach in this guide), models and collections have a much smaller feature set and the persistence of data is deferred to dedicated services.

```js
// Service oriented

serviceLocator.widgetService.read('123', function (err, data) {
  var model = new Model(serviceLocator, attributes)
  console.log('fetched!')
})

serviceLocator.widgetService.update(model.toJSON(), function (err) {
  console.log('saved!')
})
```

In a typical Backbone app, the directory structure for a component would like so:

```
models/
collections/
views/
templates/
controllers/
init.js
```

A service oriented structure is similar on the whole, but with the addition of services and the omission of collections:

```
models/
services/
views/
templates/
controllers/
init.js
```

### The benefits of services

#### Observability

A good application level service should be an event emitter. That way, any part of the application observe the services for various changes and updates without directly coupling to the service itself.

Say you have a blog CMS application. In the interface you want to keep a list of recently edited offers. To do this with a service, it would be as simple as the following:

```js
serviceLocator.articleService.on('create', function (data) {
  recentCollection.add(new ArticleModel(serviceLocator, data))
})
```

Whereas the above example could live anywhere, happily decoupled in a separate component and just requiring access to the serviceLocator, in an application with Active Collections, you would have to hook in to the exact location where articles are created (and remember there might be more than one location) adding complexity to an existing component.

#### Homogeneity

The service locator pattern with a centralised registry of application services is the go-to pattern on the server. For homogeneity, wouldn't it be nice to use the same pattern in the browser? *(obviously not if it had some major drawbacks vs. active record, of course!)*

#### Persistence and backend

The Backbone model and collections cater for the simple use case of a standard CRUD based persistence layer over AJAX. This can of course be customised or overridden, but now you're off of the beaten path and throwing out a lot of what Backbone does for you.

In service-land, each application service can have its own persistence, be it a JSON CRUD API over HTTP, websockets, localStorage in the browser, or even a third party data source. If the browser can talk to it, you can make a service wrapper around it.

[ventnor]: https://github.com/bengourley/ventnor
[merstone]: https://github.com/bengourley/merstone
[chale]: https://github.com/bengourley/chale
