> [!NOTE]  
> Package renamed [`@agrzes/pouch-rx`](https://www.npmjs.com/package/@agrzes/pouch-rx) and moved to [new repository](https://github.com/AGrzes/pouch-rx). This repository is no longer maintained. Please use the new package instead.

# Ouch Rx [![Build Status](https://travis-ci.org/AGrzes/ouch-rx.svg?branch=master)](https://travis-ci.org/AGrzes/ouch-rx) [![Coverage Status](https://coveralls.io/repos/github/AGrzes/ouch-rx/badge.svg?branch=master)](https://coveralls.io/github/AGrzes/ouch-rx?branch=master)
Library wrapping [PouchDB](https://pouchdb.com/) with [rxjs](https://rxjs-dev.firebaseapp.com/) observables.

## Usage
To use Ouch Rx one have to wrap database object with Ouch instance and then use its methods to create Observables and Observers.

```javascript    
const {Ouch} = require('ouch-rx');
const ouch = new Ouch(db);
ouch.all().pipe(transform).subscribe(ouch.sink())
```
## Reference
### Constructor
Wraps single pouchdb database.
```javascript   
new Ouch(db)
```
|Argument| Description|
|---|---|
| db | A PouchDB database |

### Methods
#### all
Returns observable of all documents.
```javascript   
ouch.all(options)
```
|Argument| Description|
|---|---|
| options | An options object passed to db.all_docs. The following fields are not passed: `include_docs` |

#### changes
Returns observable of a change feed.
```javascript   
ouch.changes(options)
```
|Argument| Description|
|---|---|
| options | An options object passed to db.changes.|

#### view
Returns observable of view results.
```javascript   
ouch.view(name,options)
```
|Argument| Description|
|---|---|
| name | A view name |
| options | An options object passed to db.query. The following fields are not passed: `include_docs` |

To use this method the db object must support query method.

#### sink
Returns observer that writes incoming objects into db. 

The operation will fail on any error so it is useful for inserting completely new documents and for updating documents previously fetched from db (so current `_rev` is known). 
```javascript   
ouch.sink()
```
#### merge
Returns observer that writes incoming objects into db. 

The operation will call the merge function with incoming object to prepare document to store. The operation will call the merge function again with incoming object and current document state if conflict is encountered. It will then retry write with the result of merge function.
```javascript   
ouch.merge(mergeFunction)    
```
|Argument| Description|
|---|---|
| mergeFunction | A merge function `(object,current) => document to store`  |

### Merge Functions
The following ready to use merge functions are provided
* `skip` - if document exist in database then skip update
* `override` - override document in database with new document
* `assign` - merge the existing and new document the way that uses new properties over existing but leave existing properties if new values are not provided. The merge is shallow, it considers only top level properties.
