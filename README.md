# deeks - Deep Object Key Extraction

[![Dependencies](https://img.shields.io/david/mrodrig/deeks.svg)](https://www.npmjs.org/package/deeks)
[![Downloads](http://img.shields.io/npm/dm/deeks.svg)](https://www.npmjs.org/package/deeks)
[![NPM version](https://img.shields.io/npm/v/deeks.svg)](https://www.npmjs.org/package/deeks)
[![Known Vulnerabilities](https://snyk.io/test/npm/deeks/badge.svg)](https://snyk.io/test/npm/deeks)
[![Package Size](https://img.shields.io/bundlephobia/min/deeks.svg)](https://www.npmjs.org/package/deeks)

[![Build Status](https://travis-ci.org/mrodrig/deeks.svg?branch=master)](https://travis-ci.org/mrodrig/deeks)
[![Maintainability](https://api.codeclimate.com/v1/badges/830bc7f29f61466986ac/maintainability)](https://codeclimate.com/github/mrodrig/deeks/maintainability)
[![Test Coverage](https://api.codeclimate.com/v1/badges/830bc7f29f61466986ac/test_coverage)](https://codeclimate.com/github/mrodrig/deeks/test_coverage)
[![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=rodrigues.mi%40husky.neu.edu&item_name=Open+Source+Software+Development+-+Node+Modules&currency_code=USD&source=url)

**Retrieve all keys and nested keys from objects and arrays of objects.**

## Installing

```bash
npm install --save deeks
```

Example: 
```javascript
let keys = require('deeks'),
	docPath = require('doc-path');

let generatedKeys = keys.deepKeys({
	make: 'Nissan',
	model: 'GT-R',
	trim: 'NISMO',
	specifications: [
	    {mileage: 10},
	    {cylinders: 6}
	]
}, {
    expandArrayObjects: true,
    ignoreEmptyArraysWhenExpanding: true
});
// => ['make', 'model', 'trim', 'specifications.mileage', 'specifications.cylinders']

generatedKeys.forEach((key) => 
    console.log(
        docPath.evaluatePath(key)
    )
)
// Console Output:
// Nissan
// GT-R
// NISMO
// 10
// 6
```

## API

### deepKeys(object) 

`keys.deepKeys(object, options)`

Returns all keys in an object, even if they're nested several layers deep. 
The array of keys that is returned can then be used with the 
[`doc-path`](https://github.com/mrodrig/doc-path) module to get and set values 
at a specific key path.

Options (optional):
- expandArrayObjects - `Boolean` (Default: `false`) - Should objects appearing in arrays in the provided 
object also be expanded, such that keys appearing in those objects are extracted and 
included in the returned key path list?
	- Example:
	```json
	{
		"make": "Nissan",
		"model": "GT-R",
		"trim": "NISMO",
		"specifications": [
			{"mileage": 10},
			{"cylinders": 6}
		]
	}
	```
	- expandArrayObjects = `false` results in: `['make', 'model', 'trim', 'specifications']`
	- expandArrayObjects = `true` results in: `['make', 'model', 'trim', 'specifications.mileage', 'specifications.cylinders']`
- ignoreEmptyArraysWhenExpanding - `Boolean` (Default: `false`) - Should empty array keys be ignored when expanding array objects?
	- Note: This only has an effect when used with `expandArrayObjects`.
	- Example:
	```json
	{ 
		"features": [ {"name": "A/C" }],
		"rebates": []
	}
	```
	- ignoreEmptyArraysWhenExpanding = `false` results in: `['features.name', 'rebates']`
	- ignoreEmptyArraysWhenExpanding = `true` results in: `['features.name']`

Returns: `Array[String]`

Example: `['make', 'model', 'specifications.odometer.miles', 'specifications.odometer.kilometers']`

### deepKeysFromList(array) 

`keys.deepKeysFromList(array)`

Returns all keys in each object in the array, even if the keys are nested 
several layers deep in each of the documents. These can also be used with the 
[`doc-path`](https://github.com/mrodrig/doc-path) module.

Options (optional):
- expandArrayObjects - `Boolean` (Default: `false`) - Should objects appearing in arrays in the provided 
object also be expanded, such that keys appearing in those objects are extracted and 
included in the returned key path list?
	- Example:
	```json
	{
		"make": "Nissan",
		"model": "GT-R",
		"trim": "NISMO",
		"specifications": [
			{"mileage": 10},
			{"cylinders": 6}
		]
	}
	```
	- expandArrayObjects = `false` results in: `['make', 'model', 'trim', 'specifications']`
	- expandArrayObjects = `true` results in: `['make', 'model', 'trim', 'specifications.mileage', 'specifications.cylinders']`
- ignoreEmptyArraysWhenExpanding - `Boolean` (Default: `false`) - Should empty array keys be ignored when expanding array objects?
	- Note: This only has an effect when used with `expandArrayObjects`.
	- Example:
	```json
	[
		{ "features": [ {"name": "A/C" }] },
		{ "features": [] }
	] 
	```
	- ignoreEmptyArraysWhenExpanding = `false` results in: `['features.name', 'features']`
	- ignoreEmptyArraysWhenExpanding = `true` results in: `['features.name']`

Returns: `Array[Array[String]]`

Example: `[ ['make', 'model', 'specifications.odometer.miles', 'specifications.odometer.kilometers'] ]`

## Examples

This module integrates really nicely with the 
[`doc-path`](https://github.com/mrodrig/doc-path) module, which allows
the programmatic getting and setting of key paths produced by this module.

Here's an example of how this works:

```javascript
const path = require('doc-path'),
      keys = require('deeks');

let car = {
		make: 'Nissan',
		model: 'GT-R',
		trim: 'NISMO',
		specifications: {
			mileage: 10,
			cylinders: '6'
		}
	},
	
	carKeys = keys.deepKeys(car);

for(let keyPath of carKeys) {
    // Clear all values
    path.setPath(car, keyPath, '');
}
```

## Tests

```bash
$ npm test
```

_Note_: This requires `mocha`, `should`, and `underscore`.

To see test coverage, please run:
```bash
$ npm run coverage
```

Current Coverage is:
```
Statements   : 100% ( 46/46 )
Branches     : 100% ( 30/30 )
Functions    : 100% ( 9/9 )
Lines        : 100% ( 45/45 )
```
