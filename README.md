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

```c++
struct Palette_RGB565 {
    uint8_t palette_id;
    uint8_t flags;
    uint16_t colors[];
};

void Deserialize_Palette_RGB565(Palette_RGB565& output, std::span<uint8_t const> raw_bytes);
void Serialize_Palette_RGB565(std::span<uint8_t> buffer, Palette_RGB565 const& input);
```

Thanks to [Hy](https://hylang.org), the user-written definitions can also be directly imported as Python modules.

```python
import hy, pathlib
from my_structs import Palette_RGB565

pathlib.Path("palette.bin").write_bytes(
    Palette_RGB565(palette_id=23, flags=0xC0, colors=[0x0000, 0x5555, 0xAAAA, 0xFFFF]).tobytes()
)
```

The motivation for this project is to reduce repeated work when interoperating between Python and C++.

#### Q: When would this be more appropriate than Protobuf/FlatBuffers/Cap'n Proto?

A: When you need absolute minimum overhead.
You have full control over the "wire layout" and for fixed-size structures, they can be just `fread` directly into memory.
This is useful on tightly constrained platforms.
