Ember API Store
---------------

Storage adapter for [Ember](http://emberjs.com) to [compatible APIs](http://github.com/rancherio/api-spec).

## Installation
```bash
  npm install --save-dev ember-api-store
```

Create an initializer in your application:
```javascript
  export function initialize(container, application) {
    import Store from "ember-api-store";
    var store = Store.create({
      baseUrl: '/v1'
      container: container
    });

    application.register('store:main', store, {instantiate: false});
    application.inject('controller', 'store', 'store:main');
    application.inject('route', 'store', 'store:main');
    application.inject('model', 'store', 'store:main');
  }

  export default {
    name: 'store',
    initialize: initialize
  };
```

## Usage

### Store

The store performs all communication with the API service and maintains a single copy of all the resources that come back from it.  This ensures that changes to a resource in one place propagate propertly to other parts of your application that use the same resource.

Methods:
* `find(type [,id] [,options])`: Query API for records of `type`, optionally with `id` and other `options` like `filter` and `include`.  Returns a promise.
* `getById(type, id)`: Get a record from the local cache by `type` and `id`.  Returns a resource or undefined synchronously.
* `hasRecordFor(type, id)`: Returns true if a record for `type` and `id` exists in cache synchronously.
* `all(type)`: Returns a "live" array of all the records for [type] in the store.  The array will be updated as records are added and removed from the store.
* `createRecord(data)`: Create a record given fields `data`.  If the data contains an id and a self link, it is added to the store.  Otherwise you must call `.save()` to add to the store.  Returns a `Resource`.
* `removeRecord(type, id)`: Remove a record from the store given `type` and `id.  Calls `.delete()`  on the record and does not remove until it succeeds.  Returns a promise.

### Resource
A resource is a model object representing a single resource in the API.

Methods:
* `.merge(data)`: Take the values in `data` and replace the corresponding values in this resource with them.  Returns the resource so you can chain calls.
* `.replaceWith(data)`: Replace all the values in this resource with the ones in `newData`.  Returns the resource so you can chain calls.
* `.clone()`: Returns a duplicate of this resource.  Changes to the clone will not initially affect the original.  If `.save()` is called on the clone, the response data will be merged into both the clone and original.
* `.hasLink(name)`: Returns a boolean for whether this resource has a link with the given `name` or not.
* `.followLink(name [,options])`: Retrieves the link with the given `name` and returns a promise that resolves with the response data.
* `.importLink(name [,options])`: Retrieves the link with the given `name` and assigns the response data as a property with the same `name` on the resource.  Returns a promise that resolves with the resource.
* `.hasAction(name)`: Returns a boolean for whether this resource has an action with the given `name` or not.
* `.doAction(name [,data])`: Performs the action given by `name`, optionally sending `data` and returns a promise that resolves with the response data.
* `.save()`: Sends the resource to the API to persist it.  On success, adds the resource to the store if it wasn't already in there.  Returns a promise that resolves to the resource, and also updates the store record with the response data if it is provided.
* `.delete()`: Sends a delete request to the API to remove a resource.  On success, the resource is removed from the store if it was in it.
* `.serialize()`: Returns a plain JavaScript object representation of the resource.

### Collection
A collection is a model object representing an array of resources in the API.  It is an ArrayProxy that proxies array requests to the `data` elements of the collection, but collections are themselves resources that may have links and actions themselves that you can use (as a resource, above).

* `.serialize()`: Returns a plain JavaScript array representation of the collection.

## Developing

* `git clone` this repository
* `npm install`
* `bower install`

### Running

* `ember server`
* Visit your app at http://localhost:4200.

### Running Tests

* `ember test`
* `ember test --server`

### Building

* `ember build`

License
=======
Copyright (c) 2014 [Rancher Labs, Inc.](http://rancher.com)

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

[http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.