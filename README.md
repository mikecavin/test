# Keyhunt - RMD160 and BSGS Modes

This repository contains an extraction of the **rmd160** and **bsgs** modes from the [keyhunt](https://github.com/albertobsd/keyhunt) tool by albertobsd.

## Overview

Keyhunt is a tool for searching private keys for cryptocurrencies that use the secp256k1 elliptic curve. This extraction focuses specifically on two powerful search modes:

1. **RMD160 Mode** - Searches for RIPEMD-160 hashes instead of full addresses
2. **BSGS Mode** - Baby-step giant-step algorithm for efficient key searching

All original functionality has been preserved without modifications to the internal logic or algorithms.

## Modes Included

### RMD160 Mode

The rmd160 mode works similarly to address mode, but targets hash rmd160 values directly instead of full addresses. This can be faster as it bypasses address encoding steps.

**Example usage:**
```bash
./keyhunt -m rmd160 -f tests/1to32.rmd -r 1:FFFFFFFF -l compress -s 5
```

**Test your luck with puzzle #66:**
```bash
./keyhunt -m rmd160 -f tests/66.rmd -b 66 -l compress -R -q
```

### BSGS Mode

Baby-step giant-step is an optimized algorithm for searching within a known range. It's particularly effective for larger bit ranges.

**Example usage:**
```bash
./keyhunt -m bsgs -f tests/125.txt -b 125 -q -s 10 -R
```

Add `-t numberThreads` and `-k factor` for better performance.

## Building

### Requirements

On Debian-based systems:
```bash
apt update && apt upgrade
apt install git -y
apt install build-essential -y
apt install libssl-dev -y
apt install libgmp-dev -y
```

### Compilation

**Main version (recommended):**
```bash
make
```

**Legacy version (if main version has issues):**
```bash
make legacy
```

**BSGS standalone version:**
```bash
make bsgsd
```

### Running

After compilation:
```bash
./keyhunt -h
```

## Directory Structure

```
.
├── keyhunt.cpp           # Main program with both modes integrated
├── bsgsd.cpp             # Standalone BSGS implementation
├── keyhunt_legacy.cpp    # Legacy version using GMP
├── Makefile              # Build configuration
├── base58/               # Base58 encoding/decoding
├── bloom/                # Bloom filter implementation (new)
├── oldbloom/             # Bloom filter implementation (legacy)
├── rmd160/               # RIPEMD-160 implementation
├── hash/                 # Hash functions (ripemd160, sha256, SSE versions)
├── secp256k1/            # Elliptic curve operations
├── sha3/                 # SHA3 and Keccak implementations
├── xxhash/               # xxHash implementation
├── util.c/h              # Utility functions
├── tests/                # Test files and puzzle data
└── gmp256k1/             # GMP-based elliptic curve (legacy)
```

## Key Components

### RMD160 Components
- `rmd160/rmd160.c` - RIPEMD-160 core implementation
- `rmd160/rmd160.h` - Header file
- `hash/ripemd160.cpp` - C++ wrapper
- `hash/ripemd160_sse.cpp` - SSE-optimized version
- Integrated in `keyhunt.cpp` as `MODE_RMD160`

### BSGS Components
- BSGS logic integrated directly in `keyhunt.cpp` as `MODE_BSGS`
- `bsgsd.cpp` - Standalone BSGS implementation
- `struct bsgs_xvalue` - Data structure for BSGS table
- Multiple threading strategies: forward, backward, both, random, dance
- Sorting algorithms optimized for BSGS tables

### Shared Dependencies
- **secp256k1/** - All elliptic curve operations (Int, Point, SECP256K1, IntMod, Random, IntGroup)
- **hash/** - SHA256 and RIPEMD-160 with SSE optimizations
- **bloom/** - Efficient bloom filters for target lookup
- **base58/** - Base58 encoding for addresses
- **xxhash/** - Fast hashing for bloom filters
- **sha3/** - Keccak/SHA3 for Ethereum support

## Test Files

The `tests/` directory contains various test files:
- `1to32.txt`, `1to32.rmd` - First 32 puzzles
- `66.txt`, `66.rmd` - Puzzle #66
- `125.txt`, `130.txt` - Higher puzzles
- `substracted40.txt` - Substracted keys for xpoint mode
- `unsolvedpuzzles.txt`, `unsolvedpuzzles.rmd` - Unsolved puzzles

## Options

Common options for both modes:

```
-m mode      Mode of search (bsgs, rmd160, etc.)
-f file      File with target data
-b bits      Bit range (e.g., -b 66 for puzzle 66)
-r range     Range in format start:end (e.g., -r 1:FFFFFFFF)
-t threads   Number of threads
-l type      compress, uncompress, or both
-R           Random mode
-q           Quiet thread output
-s seconds   Stats output interval
-e           Enable endomorphism (not for BSGS)
-k factor    BSGS table size factor
```

## Performance Tips

### For RMD160 Mode:
- Use `-l compress` if searching only compressed keys
- Add `-e` for endomorphism speedup
- Use multiple threads with `-t`
- Random mode `-R` for broad searches

### For BSGS Mode:
- Adjust `-k` factor to balance memory vs speed
- Use `-t` for multiple threads
- Larger `-k` values use more RAM but can be faster
- Monitor with `-s` for regular statistics

## Documentation

- `BSGSD.md` - Detailed BSGS documentation
- `CHANGELOG.md` - Version history and changes
- `TODO.md` - Future improvements
- `LICENSE` - License information

## Original Source

This is an extraction from the original keyhunt project:
- Repository: https://github.com/albertobsd/keyhunt
- Author: albertobsd
- Forum: https://bitcointalk.org/index.php?topic=5322040.0

## License

This code maintains the original license from keyhunt. See the LICENSE file for details.

## Donations

This is a hobby project by the original author. If you find it useful, consider supporting:
https://github.com/albertobsd/keyhunt#donations

## Disclaimer

This tool was made as a generic tool for Bitcoin puzzles. The author recommends everyone to stay with puzzles and use this program responsibly.

## Credits

All credit goes to albertobsd for the original keyhunt implementation. This repository is simply an extraction focusing on the rmd160 and bsgs modes with all dependencies intact.
