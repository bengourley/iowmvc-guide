# IOWMVC

This is a a guide on how to use the following set of modules together to make
frontend JS applications:

- [ventnor views][ventnor]
- [merstone models][merstone]
- [chale collections][chale]

## TOC

- [Philosophy](#philosophy)
- [Structuring an application](#structuring-an-application)

## Philosophy

These modules were born from an admiration of Backbone (and the proliferation
of other JS frameworks) that brought decent application structure and useful
bases for frontend development to the browser wilderness.

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

### S-MVC


[ventnor]: https://github.com/bengourley/ventnor
[merstone]: https://github.com/bengourley/merstone
[chale]: https://github.com/bengourley/chale
