browserify-bower
================

[![NPM](https://nodei.co/npm/browserify-bower.png)](https://nodei.co/npm/browserify-bower/)

_(former name `brbower`)_

Let `browserify-bower` plugin require bower components for you when building bundles, then you can `require` them as normal node modules in application codes.  
You can also provide external config, to guide `browserify-bower` to external some bower components, which is useful when when building multiple bundles.


# install

With [npm](https://npmjs.org) do:

```
npm install browserify-bower
```

# usage
## Programmatic API
In your task runner like `gulp`, add this plugin to `browserify`:
```javascript
b.plugin('browserify-bower', {
	require: ['*', 'base62/lib/base62'],
	external: {
		exclude: ['comp1', 'comp2']
	}
});
```
_p.s. of course, you can also configure this in node `package.json`._

Then, in js or html codes, you can require it like normal node module:
```
// in xxx.js
var comp1 = require('comp1');
var comp2 = require('alias2');
...

// in xx.html
<div class="container-fluid">
...
</div>
<script type="text/javascript">
  require('domready')(function() {
    var comp1 = require('comp1');
    ...
  });
</script>
```

## Command Line
Use conf file,
```shell
$ browserify entry.js -d -p [browserify-bower --conf conf.json] > bundle.js
```
Use a node of the conf json,
```shell
$ browserify entry.js -d -p [browserify-bower --conf conf.json --confnode aa.bbb] > bundle.js
```

## workdir
By default, `browserify-bower` will try to find the working bower components dir from the dir of `process.cwd()`. But you can specify another one.

In programmatic API, pls use like `b.plugin(browserifyBower.workdir(thedir), {..})`.
In command line, pls use parameter `--workdir thedir`.


> p.s. feel free to use it side by other plugins/transforms, since it's a standard [`browserify`](https://github.com/substack/node-browserify) plugin, no hack, no change to your codes.

# options
![browserify-bower config](https://raw.githubusercontent.com/tminglei/browserify-bower/master/doc/browserify-bower-config.png)

**action:** _string_, guide `browserify-bower` to **require**/**external** specified bower components; available values: `require` | `external`, default `require`  

**action config:** _string array or map object_, available config items: `include` | `exclude` | `alias`, examples:  
a) `['name1', 'name2', ...]` _(p.s. will be treated as `{ include: [name1, name2, ...] }`)_  
b) `{ exclude: ['comp5', 'comp7'], alias: ['comp1:alias1'] }`

_Notes: `name` format: `name[:alias]`, and name can be component name or submodule like 'base62/lib/base62'._

#### _Additional Rules:_
- if options undefined, `{ require: [all bower dependency names] }` will be used
- if options..include undefined, `[all bower dependency names]` will be used
- if both include/exclude and alias declared an alias for a component, declaration in alias will be used

# run test
_You need ensure related node modules (for `browserify-bower`) and bower components (for test codes) installed, then run `npm test`._

For first time, you can do it like this:
```sh
browserify-bower $ npm install
...
browserify-bower $ cd test
browserify-bower/test $ bower install
...
browserify-bower/test $ cd ..
browserify-bower $ npm test

	> browserify-bower@0.4.0 test ~/repos/browserify-bower
	> mocha


	  ....

	  4 passing (580ms)

browserify-bower $
```
# diffenence with `debowerify`
`browserify-bower` and `debowerify` try to resolve same problem, but by different ways.  
_(p.s. in fact, browserify-bower's test codes were copied and modified from `debowerify`, thanks so much ^^)_

**debowerify's way:** analyze every js files of the application, to find/replace require string for bower components with their real paths  
**browserify-bower's way:** pre resolve specified bower components and require them to browserify, then when required, they're already there

#### Comparison of `browserify-bower` and `debowerify`:  
|                             |   browserify-bower            |  debowerify                                    |
| --------------------------- | ----------------------------- | ---------------------------------------------- |
| require submodules <br> _(in application codes)_ | support <br> _(built-in)_ | support <br> _(built-in)_ |
| require ... in html/template files | OK               | not OK <br> _(since it doesn't anaylze html/template files)_ |
| individual require/external <br> _(in build scripts)_ | easy <br> _(with options)_ | not so easy <br> _(through `bower-resolve`)_ |
| extension type              | plugin                        | transform                                                           |
| work mode                   | synchronous                   | asynchronous <br> _(since it depends on bower's resolving results)_ |
| performance                 | slight and quickly <br> _(~ 2s to build a project of mine)_ | slowly <br> _(13 ~ 14s to build the same project)_ <br> _(since it analyzes every js files of the application)_ |


# history
v0.4.0 (20-Dec-2014):  
1) add command line support  

v0.3.0 (14-Aug-2014):  
1) built-in support for submodules  
2) enhancement: if bower.main undefined, check 'index.js' then 'compname'.js

v0.2.0 (25-July-2014):  
1) added tests  
2) document improvement  
3) logic change: include all components declared in `dependencies` and `devDependencies` of bower.json, not only `dependencies`, if options..include undefined  
4) enhancement: allow to specify workdir, where to determine bower components' home dir; default `process.cwd()`

v0.1.0 (22-July-2014):  
1) first release (works fine in my personal project)

# license

MIT
