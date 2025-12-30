# Verification Checklist

This document provides a comprehensive checklist to verify that the extraction is complete and functional.

## ✅ Build Verification

### Main Version
- [ ] Run `make`
- [ ] Check for `keyhunt` executable
- [ ] Verify size is ~350-400 KB
- [ ] No critical compilation errors

**Test Command:**
```bash
make && ls -lh keyhunt
```

**Expected Result:**
```
-rwxr-xr-x 1 user user 358K keyhunt
```

### BSGS Standalone
- [ ] Run `make clean && make bsgsd`
- [ ] Check for `bsgsd` executable
- [ ] Verify size is ~250-300 KB
- [ ] No critical compilation errors

**Test Command:**
```bash
make clean && make bsgsd && ls -lh bsgsd
```

**Expected Result:**
```
-rwxr-xr-x 1 user user 278K bsgsd
```

### Legacy Version (Optional)
- [ ] Install libssl-dev and libgmp-dev
- [ ] Run `make clean && make legacy`
- [ ] Check for `keyhunt` executable
- [ ] No critical compilation errors

**Test Command:**
```bash
sudo apt install -y libssl-dev libgmp-dev
make clean && make legacy && ls -lh keyhunt
```

## ✅ RMD160 Mode Verification

### Test 1: Basic Functionality
**Command:**
```bash
./keyhunt -m rmd160 -f tests/1to32.rmd -r 1:100 -l compress -q
```

**Expected Output:**
```
[+] Mode rmd160
[+] Search compress only
Hit! Private Key: 1
pubkey: 0279be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798
Address 1BgGZ9tcN4rm9KBzDn7KprQz87SZ26SAMH
rmd160 751e76e8199196d454941c45d1b3a323f1433bd6
```

**Checklist:**
- [ ] Mode rmd160 loads successfully
- [ ] File loads without errors
- [ ] At least key 1 is found
- [ ] Output includes private key, pubkey, address, and rmd160

### Test 2: Random Search
**Command:**
```bash
timeout 10 ./keyhunt -m rmd160 -f tests/66.rmd -b 66 -l compress -R -q -s 5
```

**Expected Output:**
```
[+] Mode rmd160
[+] Random mode
[+] Bit Range 66
Total X keys in Y seconds: ~Z Mkeys/s
```

**Checklist:**
- [ ] Random mode activates
- [ ] Search runs without crashes
- [ ] Statistics output appears
- [ ] Keys/second rate is displayed

### Test 3: Multi-threading
**Command:**
```bash
timeout 5 ./keyhunt -m rmd160 -f tests/1to32.rmd -r 1:10000 -l compress -t 4 -q -s 2
```

**Expected Output:**
```
[+] Threads : 4
Total X keys in Y seconds
```

**Checklist:**
- [ ] Multi-threading enabled
- [ ] Search completes faster than single thread
- [ ] No threading errors

### Test 4: Endomorphism
**Command:**
```bash
timeout 5 ./keyhunt -m rmd160 -f tests/1to32.rmd -r 1:10000 -l compress -e -q
```

**Expected Output:**
```
[+] Endomorphism enabled
```

**Checklist:**
- [ ] Endomorphism activates
- [ ] Speed approximately doubles
- [ ] No errors

## ✅ BSGS Mode Verification

### Test 1: Basic Functionality
**Command:**
```bash
./keyhunt -m bsgs -f tests/125.txt -b 50 -q
```

**Expected Output:**
```
[+] Mode BSGS sequential
[+] Opening file tests/125.txt
[+] Added 1 points from file
[+] Bit Range 50
```

**Checklist:**
- [ ] BSGS mode loads successfully
- [ ] Public key file loads correctly
- [ ] BSGS initializes without errors
- [ ] Search begins

### Test 2: Random BSGS
**Command:**
```bash
timeout 10 ./keyhunt -m bsgs -f tests/125.txt -b 50 -R -q -s 5
```

**Expected Output:**
```
[+] Mode BSGS random
Total X keys in Y seconds
```

**Checklist:**
- [ ] Random BSGS mode activates
- [ ] Search runs without crashes
- [ ] Statistics appear

### Test 3: Custom K Factor
**Command:**
```bash
./keyhunt -m bsgs -f tests/125.txt -b 50 -k 1024 -q
```

**Expected Output:**
```
[+] K factor: 1024
```

**Checklist:**
- [ ] K factor setting accepted
- [ ] BSGS table allocates correctly
- [ ] No memory errors

### Test 4: Multi-threading BSGS
**Command:**
```bash
timeout 5 ./keyhunt -m bsgs -f tests/125.txt -b 50 -t 4 -q
```

**Expected Output:**
```
[+] Threads : 4
```

**Checklist:**
- [ ] Multi-threading works with BSGS
- [ ] No thread conflicts
- [ ] Search runs smoothly

### Test 5: Standalone BSGS
**Command:**
```bash
timeout 5 ./bsgsd -f tests/125.txt -b 50 -t 2
```

**Expected Output:**
```
(BSGS initialization and search begins)
```

**Checklist:**
- [ ] Standalone bsgsd executable works
- [ ] File loads correctly
- [ ] Search runs

## ✅ File Structure Verification

### Source Files
- [ ] keyhunt.cpp exists
- [ ] bsgsd.cpp exists
- [ ] keyhunt_legacy.cpp exists
- [ ] Makefile exists

### RMD160 Files
- [ ] rmd160/rmd160.c exists
- [ ] rmd160/rmd160.h exists
- [ ] hash/ripemd160.cpp exists
- [ ] hash/ripemd160.h exists
- [ ] hash/ripemd160_sse.cpp exists

### BSGS Dependencies
- [ ] secp256k1/ directory complete
- [ ] hash/ directory complete
- [ ] bloom/ directory exists
- [ ] oldbloom/ directory exists

### Support Libraries
- [ ] base58/ directory exists
- [ ] xxhash/ directory exists
- [ ] sha3/ directory exists
- [ ] util.c and util.h exist

### Test Files
- [ ] tests/1to32.rmd exists
- [ ] tests/66.rmd exists
- [ ] tests/125.txt exists
- [ ] tests/1to32.txt exists

### Documentation
- [ ] README.md exists and is comprehensive
- [ ] EXTRACTION.md exists with technical details
- [ ] QUICKSTART.md exists with examples
- [ ] SUMMARY.md exists
- [ ] VERIFICATION.md (this file) exists
- [ ] LICENSE exists
- [ ] BSGSD.md exists
- [ ] CHANGELOG.md exists

### Build Configuration
- [ ] .gitignore exists
- [ ] .gitignore excludes executables
- [ ] .gitignore excludes object files
- [ ] .gitignore excludes output files

## ✅ Feature Completeness Verification

### RMD160 Mode Features
- [ ] Load rmd160 hashes from file
- [ ] Search compressed keys
- [ ] Search uncompressed keys
- [ ] Search both types
- [ ] Random search mode
- [ ] Sequential range search
- [ ] Bit range specification (-b)
- [ ] Explicit range specification (-r)
- [ ] Bloom filter optimization
- [ ] Endomorphism support (-e)
- [ ] Multi-threading (-t)
- [ ] Statistics output (-s)
- [ ] Quiet mode (-q)
- [ ] Key found output
- [ ] File output (KEYFOUNDKEYFOUND.txt)

### BSGS Mode Features
- [ ] Load public keys from file
- [ ] Sequential forward search
- [ ] Sequential backward search (-B backward)
- [ ] Bidirectional search (-B both)
- [ ] Random search (-B random)
- [ ] Dance pattern search (-B dance)
- [ ] Configurable table size (-k)
- [ ] Save BSGS tables (-S)
- [ ] Load BSGS tables
- [ ] Skip checksum (-6)
- [ ] Multi-threading (-t)
- [ ] Statistics output (-s)
- [ ] Quiet mode (-q)
- [ ] Bit range specification (-b)
- [ ] Key found output

### Supporting Features
- [ ] secp256k1 operations work
- [ ] Big integer math works
- [ ] Point operations work
- [ ] RIPEMD-160 hashing works
- [ ] SHA-256 hashing works
- [ ] SSE optimizations compile
- [ ] Base58 encoding works
- [ ] Bloom filters work
- [ ] xxHash works
- [ ] Random generation works

## ✅ Code Integrity Verification

### Original Code Preserved
- [ ] No modifications to algorithms
- [ ] No changes to data structures
- [ ] Original comments preserved
- [ ] Original file structure maintained
- [ ] Compilation flags unchanged

### Extraction Quality
- [ ] All dependencies included
- [ ] All headers present
- [ ] All source files present
- [ ] Build system complete
- [ ] Test files included

## ✅ Documentation Verification

### README.md
- [ ] Describes both modes
- [ ] Includes usage examples
- [ ] Lists all options
- [ ] Explains directory structure
- [ ] Has build instructions
- [ ] Credits original author

### EXTRACTION.md
- [ ] Details extraction process
- [ ] Lists all extracted files
- [ ] Explains code structure
- [ ] Documents dependencies
- [ ] Shows verification results

### QUICKSTART.md
- [ ] Has installation instructions
- [ ] Includes quick examples
- [ ] Explains common options
- [ ] Shows file formats
- [ ] Lists performance tips

### SUMMARY.md
- [ ] Summarizes extraction
- [ ] Lists completion status
- [ ] Shows test results
- [ ] Documents features

## ✅ Git Repository Verification

### Branch
- [ ] On correct branch: feat-extract-keyhunt-rmd160-bsgs-to-test-repo
- [ ] All files tracked
- [ ] No unwanted files in staging

### .gitignore
- [ ] Excludes keyhunt executable
- [ ] Excludes bsgsd executable
- [ ] Excludes *.o files
- [ ] Excludes output files
- [ ] Includes all source files

### Repository State
- [ ] Clean working directory (except new files)
- [ ] All source files ready to commit
- [ ] Documentation complete
- [ ] Ready for push

## ✅ Final Checks

### Compilation
- [ ] Main version compiles without errors
- [ ] BSGS standalone compiles without errors
- [ ] Only expected warnings (if any)

### Execution
- [ ] RMD160 mode runs successfully
- [ ] BSGS mode runs successfully
- [ ] No crashes or segfaults
- [ ] Help text displays correctly

### Performance
- [ ] RMD160 speed is reasonable (~4-5 Mkeys/s)
- [ ] BSGS initializes correctly
- [ ] Multi-threading works
- [ ] No memory leaks (during basic testing)

### Documentation
- [ ] All documentation files present
- [ ] No broken links
- [ ] Examples are accurate
- [ ] Instructions are clear

### Original Functionality
- [ ] All RMD160 features preserved
- [ ] All BSGS features preserved
- [ ] All dependencies functional
- [ ] Test files work correctly

## Status Summary

After completing all checks above:

**Extraction Status:** [ ] COMPLETE / [ ] INCOMPLETE / [ ] ISSUES FOUND

**Issues Found:**
```
(List any issues discovered during verification)
```

**Resolution:**
```
(Describe how issues were or will be resolved)
```

## Notes

Use this checklist to verify the extraction at any time. All items should be checked before considering the extraction complete.

To perform a quick verification, focus on:
1. Build Verification (both main and bsgsd)
2. RMD160 Mode Test 1
3. BSGS Mode Test 1
4. File Structure Verification (spot check)
5. Git Repository Verification

For complete verification, check all items in sequence.
