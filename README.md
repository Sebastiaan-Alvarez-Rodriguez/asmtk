This Repo
------
This repo adds meson build to the AsmTK project.  
It can be compiled with `C++11` and up.  
It has a dependency on [asmjit](https://github.com/asmjit/asmjit).
This dependency can be resolved by adding the meson-supported asmjit [here](https://github.com/Sebastiaan-Alvarez-Rodriguez/asmjit):
Just add the provided git wrap file as `asmjit.wrap` to your project, and let meson do it's magic.

You can use this project as a git wrap, like so:
```
[wrap-git]
https://github.com/Sebastiaan-Alvarez-Rodriguez/asmtk.git
revision = head
```

Refer to the next section for links to the original project.  
All credits for making AsmJit and AsmTK to the original authors. 

From here on, the regular AsmTK README follows.


AsmTK
-----

Assembler toolkit based on AsmJit.

  * [Official Repository (asmjit/asmtk)](https://github.com/asmjit/asmtk)
  * [Official Blog (asmbits)](https://asmbits.blogspot.com/ncr)
  * [Official Chat (gitter)](https://gitter.im/asmjit/asmjit)
  * [Permissive ZLIB license](./LICENSE.md)

Introduction
------------

AsmTK is a sister project of AsmJit library, which provides concepts that are useful mostly in AOT code-generation.

Features
--------

  * Both X86 and X64 modes are supported and can be selected at runtime (i.e. they not depend on how your application is compiled).
  * Asm parser can parse everything that AsmJit provides (i.e. supports all instruction sets, named labels, etc...).
  * Asm parser can also parse instruction aliases defined by AsmTK (like `movsb`, `cmpsb`, `sal`, ...). AsmJit provides just generic `movs`, `cmps`, etc... so these are extras that are handled and recognized by AsmTK.
  * Assembles to any `BaseEmitter`, which means that you can choose between `Assembler` and `BaseBuilder` at runtime, and that the result can be post-processed as well
  * More to be added...

TODO
----

  * [ ] More aliases to some SIMD instructions (to be added).
  * [ ] Implement asmtk::Linker that will add the possibility to write shared libraries and executables.

AsmParser Usage Guide
---------------------

Assembler parsing is provided by `AsmParser` class, which emits to `BaseEmitter`:

```C++
#include <asmtk/asmtk.h>

using namespace asmjit;
using namespace asmtk;

// Used to print binary code as hex.
static void dumpCode(const uint8_t* buf, size_t size) {
  enum { kCharsPerLine = 39 };
  char hex[kCharsPerLine * 2 + 1];

  size_t i = 0;
  while (i < size) {
    size_t j = 0;
    size_t end = size - i < kCharsPerLine ? size - i : size_t(kCharsPerLine);

    end += i;
    while (i < end) {
      uint8_t b0 = buf[i] >> 4;
      uint8_t b1 = buf[i] & 15;

      hex[j++] = b0 < 10 ? '0' + b0 : 'A' + b0 - 10;
      hex[j++] = b1 < 10 ? '0' + b1 : 'A' + b1 - 10;
      i++;
    }

    hex[j] = '\0';
    puts(hex);
  }
}

int main(int argc, char* argv[]) {
  // Setup CodeHolder for X64.
  CodeInfo ci(Environment::kArchX64);
  CodeHolder code;
  code.init(ci);

  // Attach x86::Assembler `code`.
  x86::Assembler a(&code);

  // Create AsmParser that will emit to x86::Assembler.
  AsmParser p(&a);

  // Parse some assembly.
  Error err = p.parse(
    "mov rax, rbx\n"
    "vaddpd zmm0, zmm1, [rax + 128]\n");

  // Error handling (use asmjit::ErrorHandler for more robust error handling).
  if (err) {
    printf("ERROR: %08x (%s)\n", err, DebugUtils::errorAsString(err));
    return 1;
  }

  // Now you can print the code, which is stored in the first section (.text).
  CodeBuffer& buffer = code.sectionById(0)->buffer();
  dumpCode(buffer.data(), buffer.size());

  return 0;
}
```

You should check out the test directory to see how AsmTK integrates with AsmJit.

Support
-------

Please consider a donation if you use the project and would like to keep it active in the future.

  * BTC: 14dEp5h8jYSxgXB9vcjE8eh78uweD76o7W
  * ETH: 0xd4f0b9424cF31DF5a5359D029CF3A65c500a581E
  * Please contact the author if you would still like to donate through a different channel or use a different crypto-currency.

Authors & Maintainers
---------------------

  * Petr Kobalicek <kobalicek.petr@gmail.com>
