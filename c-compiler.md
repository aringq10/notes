# Include files
#include is read by C's preprocessor (gcc -E) and is substituted by the contents of that file. Preprocessor looks in a regular directory like /usr/include but custom dirs can be passed with -I (e.g. -I/home/user/.local/include).

If a symbol or function isn't declared anywhere (neither in the local .c file or in any of the included .h files), the compiler errors (e.g. error: implicit declaration).

# Libraries
After preprocessing, an object file is compiled (gcc -c) with undefined symbols which come from libraries.
To tell the compiler which libraries to use, use the -l flag. If I have a file named libfoo-1.a, then i need to pass a flag like this: -lfoo-1. Same with .so libs.
The runtime loader has a name→location cache (ld.so.cache) for system-wide .so files, for fast lookup at runtime. It covers /lib, /usr/lib, and dirs in /etc/ld.so.conf. (Build-time: the linker finds libs via -L + defaults, analogous to -I for headers.)
Truly undefined symbols after linking (neither in the local .o files or any of the passed libraries) surfaces as undefined reference.

## Static linking
Build-time linker (ld) finds libs via -L + defaults. Linker takes the used code from the lib and combines it with your .o file into a final executable. Undefined symbols are resolved since the addresses for them are now known.

## Dynamic linking
-rpath embeds the path where the runtime linker should look for custom libraries, the -L flag only tells the linker this information during build time.
Runtime loader (ld.so) finds .so via ld.so.cache + -rpath. Linker leaves the undefined symbols pointing at stubs or nothing and resolves them at runtime from the ELF's NEEDED library entries.

### Shared library naming and versioning
libfoo.so (unversioned linker name, used at build time via -l) -> libfoo.so.<major> (the soname baked into binaries and resolved at runtime; major changes only on ABI breaks) -> libfoo.so.<major>.<minor> (the real file, whose full version lets multiple versions coexist on disk).

# CLI tools
ldd - run linker .so resolver and print deps + their paths on your system.
nm - output symbol information (undefined, text, data).
readelf - inspect ELF file contents, the header, dynamic section etc.
patchelf - modify ELF file.
objdump - can parse many object format, disassemble them.
ldconfig - update linker's runtime cache (/etc/ld.so.cache).
