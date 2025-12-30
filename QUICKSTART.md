# Quick Start Guide - RMD160 and BSGS Modes

## Installation

### 1. Install Dependencies (Ubuntu/Debian)
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git build-essential
```

For legacy version only:
```bash
sudo apt install -y libssl-dev libgmp-dev
```

### 2. Clone This Repository
```bash
git clone <your-repo-url>
cd <repo-directory>
```

### 3. Build
```bash
# Build main version (recommended)
make

# Or build legacy version
make legacy

# Or build standalone BSGS
make bsgsd
```

## Quick Examples

### RMD160 Mode

**Example 1: Search small range with known results**
```bash
./keyhunt -m rmd160 -f tests/1to32.rmd -r 1:FFFFFFFF -l compress
```

This will find the first 32 Bitcoin puzzle private keys.

**Example 2: Random search on puzzle #66**
```bash
./keyhunt -m rmd160 -f tests/66.rmd -b 66 -l compress -R -q -s 10 -t 4
```

Explanation:
- `-m rmd160` - Use RMD160 mode
- `-f tests/66.rmd` - Target file with rmd160 hash
- `-b 66` - Search in 66-bit range
- `-l compress` - Search only compressed keys
- `-R` - Random search mode
- `-q` - Quiet (no per-thread output)
- `-s 10` - Show stats every 10 seconds
- `-t 4` - Use 4 threads

**Example 3: Sequential range search**
```bash
./keyhunt -m rmd160 -f tests/64.rmd -r 8000000000000000:10000000000000000 -l compress -t 8
```

### BSGS Mode

**Example 1: Search puzzle #125 with random mode**
```bash
./keyhunt -m bsgs -f tests/125.txt -b 125 -R -q -s 10 -t 4
```

Explanation:
- `-m bsgs` - Use BSGS mode
- `-f tests/125.txt` - Target file with public key
- `-b 125` - Search in 125-bit range
- `-R` - Random mode
- `-q` - Quiet mode
- `-s 10` - Stats every 10 seconds
- `-t 4` - Use 4 threads

**Example 2: BSGS with custom K factor**
```bash
./keyhunt -m bsgs -f tests/130.txt -b 130 -k 2048 -t 8 -q -s 30
```

Explanation:
- `-k 2048` - Use K factor of 2048 (more RAM, potentially faster)

**Example 3: Standalone BSGS version**
```bash
./bsgsd -f tests/125.txt -b 125 -t 4
```

## Understanding the Options

### Common Options

| Option | Description | Example |
|--------|-------------|---------|
| `-m` | Mode of search | `-m rmd160` or `-m bsgs` |
| `-f` | Target file | `-f tests/66.rmd` |
| `-b` | Bit range | `-b 66` (searches 2^65 to 2^66-1) |
| `-r` | Explicit range | `-r 1:FFFFFFFF` |
| `-t` | Number of threads | `-t 8` |
| `-l` | Key type | `-l compress`, `-l uncompress`, `-l both` |
| `-R` | Random mode | `-R` |
| `-q` | Quiet mode | `-q` |
| `-s` | Stats interval (seconds) | `-s 10` |

### RMD160-Specific Options

| Option | Description |
|--------|-------------|
| `-e` | Enable endomorphism (2x speed) |
| `-z` | Bloom filter size multiplier |

### BSGS-Specific Options

| Option | Description |
|--------|-------------|
| `-k` | BSGS K factor (table size) |
| `-S` | Save BSGS tables to files |
| `-6` | Skip SHA256 checksum on files |

## File Formats

### RMD160 Files (.rmd)
Plain text file with one rmd160 hash per line (40 hex characters):
```
751e76e8199196d454941c45d1b3a323f1433bd6
7dd65592d0ab2fe0d0257d571abf032cd9db93dc
```

### BSGS Files (.txt with public keys)
Plain text file with one public key per line (66 hex characters for compressed):
```
0233709eb11e0d4439a729f21c2c443dedb727528229713f0065721ba8fa46f00e
```

## Output

When a key is found, you'll see:
```
Hit! Private Key: 1
pubkey: 0279be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798
Address 1BgGZ9tcN4rm9KBzDn7KprQz87SZ26SAMH
rmd160 751e76e8199196d454941c45d1b3a323f1433bd6
```

The private key will also be saved to `KEYFOUNDKEYFOUND.txt` in the current directory.

## Performance Tips

### For Best Speed with RMD160
1. Use multiple threads: `-t <number_of_cpu_cores>`
2. Enable endomorphism: `-e` (searches 2 keys per operation)
3. Use compressed keys only if possible: `-l compress`
4. Adjust stats interval: `-s 30` (less frequent = slightly faster)
5. Use quiet mode: `-q` (reduces output overhead)

**Example optimized command:**
```bash
./keyhunt -m rmd160 -f tests/66.rmd -b 66 -l compress -e -R -q -s 30 -t 16
```

### For Best Results with BSGS
1. Start with default K factor, increase if you have RAM: `-k 1024` or `-k 2048`
2. Use multiple threads: `-t <number_of_cpu_cores>`
3. For repeated searches, save tables: `-S` (saves time on subsequent runs)
4. Choose appropriate bit range (BSGS is best for ranges >60 bits)

**Example optimized command:**
```bash
./keyhunt -m bsgs -f tests/125.txt -b 125 -k 2048 -t 16 -q -s 30
```

## Common Issues

### Issue: "the given range is small"
**Solution**: This is just a warning for BSGS mode. BSGS is optimized for larger ranges (>60 bits). For smaller ranges, use RMD160 or address mode instead.

### Issue: "There is no valid data in the file"
**Solution**: 
- For RMD160 mode: File must contain rmd160 hashes (40 hex chars)
- For BSGS mode: File must contain public keys (66 or 130 hex chars)

### Issue: Out of memory
**Solution**: 
- Reduce BSGS K factor: `-k 512` instead of `-k 2048`
- Close other applications
- Use a machine with more RAM

### Issue: Compile errors
**Solution**:
- Try legacy version: `make legacy`
- Ensure all dependencies are installed: `sudo apt install build-essential libssl-dev libgmp-dev`
- Check gcc/g++ version: `gcc --version` (should be 7.0 or higher)

## Verifying Installation

### Test RMD160 Mode
```bash
# This should find keys 1, 3, 7, 8, etc. from the range 1-256
./keyhunt -m rmd160 -f tests/1to32.rmd -r 1:100 -l compress -q
```

Expected output: Multiple "Hit!" messages with private keys 1, 3, 7, 8, etc.

### Test BSGS Mode
```bash
# This should initialize BSGS and start searching
./keyhunt -m bsgs -f tests/125.txt -b 50 -q -s 5
```

Expected output: BSGS initialization messages and "Total X keys in Y seconds" stats.

## Next Steps

1. Read `EXTRACTION.md` for detailed technical documentation
2. Read `BSGSD.md` for BSGS algorithm details
3. Check `tests/` directory for more example files
4. Experiment with different options and ranges
5. Join the discussion at: https://bitcointalk.org/index.php?topic=5322040.0

## Getting Help

- Check the help: `./keyhunt -h`
- Read the original README: `README.md`
- Visit the original project: https://github.com/albertobsd/keyhunt
- Forum discussion: https://bitcointalk.org/index.php?topic=5322040.0

## Safety and Ethics

This tool is designed for:
- Educational purposes
- Bitcoin puzzle challenges
- Cryptographic research
- Recovery of own lost keys (with known constraints)

**DO NOT USE** for:
- Attempting to steal others' funds
- Unauthorized access to wallets
- Any illegal activities

The tool is designed for puzzles where the challenge is publicly known and legitimate.

## Support the Original Author

If you find this useful, please support albertobsd:
- BTC: 1Coffee1jV4gB5gaXfHgSHDz9xx9QSECVW
- GitHub: https://github.com/albertobsd/keyhunt

## License

See LICENSE file for details.
