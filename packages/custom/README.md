# @jimp/custom

## Available Methods

### configure

Takes a Jimp configuration and applies it to `@jimp/core`.

Sample Jimp configuration:

```js
import types from '@jimp/types';

import bmp from '@jimp/bmp';
import jpeg from '@jimp/types';
...

configure({
  types: [types]
})

// or

configure({
  types: [bmp, jpeg, ...]
})
```

## Type Definition

To define a new Jimp image type write a function the takes the current Jimp configuration. In this function you can extend Jimp's internal data structures.

This function must return and array whose first element is the mime type and second element is an array of valid file extensions.

```js
const special = require('special-js');

const MIME_TYPE = 'image/special';

export default () => ({
  mime: [MIME_TYPE, ['spec', 'special']],

  constants: {
    MIME_SPECIAL: MIME_TYPE
  },

  decoders: {
    [MIME_TYPE]: data => special.decode(data)
  },

  encoders: {
    [MIME_TYPE]: image => special.encode(image.bitmap)
  }
});
```

### Constants

A jimp image type can expose as many constants as it wants. Each jimp type is required to expose a mime type.

```js
constants: {
  MIME_SPECIAL: MIME_TYPE
},
```

### hasAlpha

A image type can define whether it supports an alpha channel.

```js
hasAlpha: {
  MIME_SPECIAL: true
},
```

### Decoder

A function that when supplied with a buffer should return a bitmap with height and width.

```js
decoders: {
  [MIME_TYPE]: data => special.decode(data)
},
```

### Encoder

A function that when supplied with a Jimp image should return an encoded buffer.

```js
encoders: {
  [MIME_TYPE]: image => special.encode(image.bitmap)
}
```

### Class

Add class properties and function to the Jimp constructor.

```js
class: {
  _quality: 100,
  quality: function(n, cb) {
    if (typeof n !== 'number') {
      return throwError.call(this, 'n must be a number', cb);
    }

    if (n < 0 || n > 100) {
      return throwError.call(this, 'n must be a number 0 - 100', cb);
    }

    this._quality = Math.round(n);

    if (isNodePattern(cb)) {
      cb.call(this, null, this);
    }

    return this;
  }
};
```