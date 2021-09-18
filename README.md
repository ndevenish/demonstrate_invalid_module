# Demonstrating InvalidModule

Demonstration repository to show the occurence of the "InvalidModule" error I
am experiencing trying to use the [`fmt`](https://github.com/fmtlib/fmt)
library.

It builds three output targets:

| Name              | What it Does                                                                         |
| ----------------- | ------------------------------------------------------------------------------------ |
| `try`             | Links to the full `fmt::fmt` library, which causes `libfmt.a` to be built and linked |
| `try_header_only` | Uses the header-only version of the `fmt` library                                    |
| `try_intelfpga`   | Same as `try`, except adds the `-fintelfpga` flag to link options.                   |

Cloning and building this repository on an interactive (`qsub -I`) node on
Intel devcloud:

```bash
git clone --recursive https://github.com/ndevenish/demonstrate_invalid_module.git
cd demonstrate_invalid_module/
mkdir _build && cd _build
cmake ..
make -i # -i means it will ignore errors and build other targets
```

Gives the output:

```
$ make -i
[ 11%] Building CXX object fmt/CMakeFiles/fmt.dir/src/format.cc.o
[ 22%] Building CXX object fmt/CMakeFiles/fmt.dir/src/os.cc.o
[ 33%] Linking CXX static library libfmt.a
[ 33%] Built target fmt
[ 44%] Building CXX object CMakeFiles/try.dir/try.cc.o
[ 55%] Linking CXX executable try
InvalidModule: Invalid SPIR-V module: Casts from private/local/global address space are allowed only to generic

llvm-foreach:
dpcpp: error: llvm-spirv command failed with exit code 1 (use -v to see invocation)
[ 55%] Built target try
[ 66%] Building CXX object CMakeFiles/try_intelfpga.dir/try.cc.o
[ 77%] Linking CXX executable try_intelfpga
[ 77%] Built target try_intelfpga
[ 88%] Building CXX object CMakeFiles/try_header_only.dir/try.cc.o
[100%] Linking CXX executable try_header_only
[100%] Built target try_header_only
```

e.g. the header-only and -fintelfpga targets build fine, but dpcpp with no
additional flags fails at the link stage (after successfully building the
`libfmt.a`).

`dpcpp` version:
```
$ dpcpp --version
Intel(R) oneAPI DPC++/C++ Compiler 2021.3.0 (2021.3.0.20210619)
Target: x86_64-unknown-linux-gnu
Thread model: posix
InstalledDir: /glob/development-tools/versions/oneapi/2021.3.0/inteloneapi/compiler/2021.3.0/linux/bin
```
