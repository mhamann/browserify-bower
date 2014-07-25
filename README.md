brbower
=======

[![NPM](https://nodei.co/npm/brbower.png)](https://nodei.co/npm/brbower/)

Let `brbower` plugin require bower components for you when building bundles, then you can treat them as normal node modules in application codes.  
You can also provide external config, to guide `brbower` to external some bower components, which is useful when when building multiple bundles.


# install

With [npm](https://npmjs.org) do:

```
npm install brbower
```

# usage
In your task runner like `gulp`, add this plugin to `browserify`:
```javascript
b.plugin('brbower', {
	require: ['comp1', 'comp2:alias2'],
	external: {
		exclude: ['comp1', 'comp2']
	}
});
```
_p.s. of course, you can also configure it in node `package.json`._

And in application codes, you can just require it as below, not care about whether it's really an node module or a bower component:
```javascript
var comp1 = require('comp1');
var comp2 = require('alias2');
...
```

# options
![brbower config](https://raw.githubusercontent.com/tminglei/brbower/master/doc/brbower-config.png)

**action:** _string_, guide `brbower` to **require**/**external** specified bower components; available values: `require` | `external`, default `require`  

**action config:** _string array or map object_, options: `include` | `exclude` | `alias`, examples:  
a) ['comp1', 'comp2', ...]  
b) { exclude: ['comp5', 'comp7'], alias: ['comp1:alias1'] }

_Notes:_  
_1) In action config, `[name1, name2, ...]` will be treated as `{ include: [name1, name2, ...] }`;_  
_2) for above, `name` format: `name[:alias]`_

#### _Additional Rules:_
- if options undefined, `{ require: [all bower dependency names] }` will be used
- if options..include undefined, `[all bower dependency names]` will be used
- if both include/exclude and alias declared an alias for a component, declaration in alias will be used

# run test
You need ensure related node modules (for `brbower`) and bower components (for test codes) installed, then run `npm test`.

For first time, you can do it like this:
```sh
tminglei@T500 ~/repos/brbower $ npm install
...
tminglei@T500 ~/repos/brbower $ cd test
tminglei@T500 ~/repos/brbower/test $ bower install
...
tminglei@T500 ~/repos/brbower/test $ cd ..
tminglei@T500 ~/repos/brbower $ npm test

	> brbower@0.1.0 test ~/repos/brbower
	> mocha


	  ....

	  4 passing (580ms)

tminglei@T500 ~/repos/brbower $
```
# diffenence with `debowerify`
`brbower` and `debowerify` try to resolve same problem, but by different ways.  
_(p.s. in fact, brbower's test codes were copied and modified from `debowerify`, thanks so much ^^)_

debowerify's way: analyze every js files of the application, to find/replace require string for bower components with their real paths  
brbower's way: pre resolve specified bower components and require them to browserify, then when required, they're already there

#### Comparison of `brbower` and `debowerify`:
|                             |   brbower                     |  debowerify                                    |
| --------------------------- | ----------------------------- | ---------------------------------------------- |
| require submodules <br> _(in application codes)_ | support <br> _(through `brbower/utils`)_ | support <br> _(built-in)_ |
| require ... in html/template files | OK               | not OK <br> _(since it doesn't anaylze html/template files)_ |
| individual require/external <br> _(in build scripts)_ | easy <br> _(with options)_ | not so easy <br> _(through `bower-resolve`)_ |
| work mode                   | synchronous                   | asynchronous <br> _(since it depends on bower's resolving results)_ |
| performance                 | slight and quickly            | slowly <br> _(since it analyzes every js files of the application)_ |


# history
v0.1.2 (25-July-2014):  
1) include all components declared in `dependencies` and `devDependencies` of bower.json, not only `dependencies`, if options..include undefined  
2) allow to specify workdir, where to determine bower components' home dir; default `process.cwd()`  
3) document improvement

v0.1.0 (22-July-2014):  
1) first release (works fine in my personal project)

# license

MIT
