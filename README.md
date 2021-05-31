# Experimental node.js binding for libvips built on ffi

**Unmaintained** This package depends on npm packages which are now
unmaintained. It needs updating to (perhaps?) use `ffi-napi`.

# Status

It's mostly complete, it seems stable, and it passes the tests suite with no
errors or leaks. It can reliably thumbnail 10,000 jpeg images in constant 
memory and without falling over. Probably. 

# Example

```javascript
var vips = require('vips');

var image = vips.Image.newFromFile(process.argv[2]);
image = image.crop(100, 100, image.width - 200, image.height - 200);

image = image.reduce(1.0 / 0.9, 1.0 / 0.9, {kernel: 'linear'});

var mask = vips.Image.newFromArray(
  [[-1,  -1, -1], 
   [-1,  16, -1], 
   [-1,  -1, -1]], 8);
image = image.conv(mask, {precision: 'integer'});

image.writeToFile(process.argv[3]);
```

# `async`

node-vips is synchronous by default, but you can call any operation with the 
special `async` option, for example:

```javascript
image.writeToFile('file.jpg', {
  Q: 90,
  async: (err, result) => {
    if (err) {
      throw err;
    }
  }
});

image.max({
  async: (err, result) => {
    if (err) {
      throw err;
    }

    console.log('maximum value in image is ' + result);
  }
});

```

Most operations are instant -- it only makes sense to use async for things
which will trigger a pixel loop.

# References

https://github.com/node-ffi/node-ffi

https://github.com/TooTallNate/ref

https://github.com/TooTallNate/ref-array

https://github.com/TooTallNate/ref-struct

https://github.com/TooTallNate/weak

https://github.com/node-ffi/node-ffi/wiki/Node-FFI-Tutorial

# Actions

Install everything

	npm install 

Run the tests

	npm test

Run a single test with the GC enabled

	./node_modules/.bin/mocha --expose-gc test/unit/image.js 

Regenerate convenience wrappers

	vips = require('vips');
	vips.generateWrappers();

and copy output to `lib/autogen.js`.

see `example/gen-wrapper.js`.
