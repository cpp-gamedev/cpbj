# Blackjack

## Dev Notes

To configure a new build, enter the root directory, and run

```cli
cmake . -B out/debug -G Ninja -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ -DCMAKE_BUILD_TYPE=Debug -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
```

Then build the game:

```cli
cmake --build out/debug
```

This project uses the LLVM style for `clang-format`:

```cli
clang-format --style=llvm -dump-config > .clang-format
```

See the [docs](https://clang.llvm.org/extra/clang-tidy/ for all options. Run `clang-tidy -list-checks` in the command line to list all enabled checks.

```cli
clang-tidy ./src/*.?pp -dump-config -checks='clang-analyzer-*,modernize-*,performance-*,readability-*' > .clang-tidy
```

Run `clang-tidy`. Add the `-fix` option to automatically apply suggested fixes.

```cli
clang-tidy -config='' -p ./out/debug -header-filter='.*' ./src/*.?pp
```
