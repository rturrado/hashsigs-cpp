# hashsigs-cpp

A C++ implementation of WOTS+ (Winternitz One-Time Signature) scheme.

## Building

To build the library:

```bash
mkdir build
cd build
cmake ..
make
```

For release build:

```bash
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
```

## Testing

Tests are built by default. To build the library without tests:

```bash
mkdir build
cd build
cmake -DBUILD_TESTS=OFF ..
make
```

Run all tests:

```bash
cd build
make test
```

Or run the test executable directly:

```bash
./build/bin/hashsigs_tests
```

For test output and backtrace:

```bash
GTEST_COLOR=1 ./build/bin/hashsigs_tests --gtest_color=yes
```

## Development Requirements

- CMake 3.10 or higher
- C++17 or higher
- Google Test

## Project Structure

```
.
├── include/       # Header files
│   ├── constants.hpp
│   ├── public_key.hpp
│   └── wotsplus.hpp
├── res/          # Resource files
    └── vectors/
├── src/          # Implementation files
│   ├── keccak.cpp
│   └── wotsplus.cpp
└── tests/        # Test vectors and unit tests
    ├── wotsplus_test.cpp
```

## License

AGPL-3.0, see COPYING
