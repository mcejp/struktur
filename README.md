# struktur

The real code is yet to be added -- how it works is that a generic language is first used to declare data structures:

```scheme
(struct Palette-RGB565
  (palette-id   uint8)
  (flags        uint8)
  (colors       (array uint16 (length-prefix uint8)))
  )

(struct Checkerboard
  (palette      Palette-RGB565)
  (values       (array (array bool)))
  )
```

In a second step, C/C++ code is generated with native structure defintions and de/serialization code.
Thanks to [Hy](https://hylang.org), the user-written definitions can also be directly imported as Python modules.
The motivation for this project is to reduce repeated work when interoperating between Python and C++.

#### Q: When would this be more appropriate than Protobuf/FlatBuffers/Cap'n Proto?

A: When you need absolute minimum overhead.
You have full control over the "wire layout" and for fixed-size structures, they can be just `fread` directly into memory.
This is useful on tightly constrained platforms.
