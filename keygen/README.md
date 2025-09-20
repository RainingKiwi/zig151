# Keygen: A Secure ChaCha20 Key Generator

## Overview
`keygen` is a command-line tool written in [Zig](https://ziglang.org/) that generates a binary key file (`key.key`) of a specified size using the ChaCha20 stream cipher. The key and nonce are derived deterministically from a user-provided password using Argon2id, a memory-hard password hashing function, ensuring stronger resistance to brute-force attacks compared to simple hashing.

## Requirements
- **Zig Compiler**: Version 0.15.1 (stable) for Windows x86_64. Download from [ziglang.org](https://ziglang.org/download/0.15.1/zig-x86_64-windows-0.15.1.zip).
- **Windows**: Tested on Windows 10/11 (x86_64 architecture).
- **Disk Space**: Enough to store the output file (up to 20GB).
- **Memory**: Argon2 uses 64MiB of memory per key derivation (adjustable in code).

## Installation
1. Download and extract `zig-x86_64-windows-0.15.1.zip` to a directory, e.g., `C:\Users\<YourUsername>\OneDrive\Desktop\zig`.
2. Place `keygen.zig` in a project directory, e.g., `C:\Users\<YourUsername>\OneDrive\Desktop\zig1`.
3. Ensure `zig.exe` is accessible (either add to PATH or use full path).

## Usage
Compile and run the tool from the command prompt in your project directory.

### Compile
```cmd
C:\Users\<YourUsername>\OneDrive\Desktop\zig\zig.exe build-exe keygen.zig
```

### Run
```cmd
keygen.exe <size_bytes> <password>
```
- `<size_bytes>`: Size of the output file in bytes (1 to 20GB).
- `<password>`: Password to derive the key and nonce.

Example:
```cmd
keygen.exe 1024 mypassword
```
Creates a 1024-byte `key.key` file in the current directory.

### Error Handling
- If `<size_bytes>` is invalid (<1 or >20GB), prints: `Error: size must be between 1 byte and 20GB`.
- If arguments are missing, prints: `Usage: keygen <size_bytes> <password>`.

## How It Works
1. **Input Parsing**: Reads `<size_bytes>` and `<password>` from command-line arguments.
2. **Key Derivation**:
   - Generates deterministic 16-byte salts for key and nonce using SHA-256 (password for key, password + "keygen-nonce" for nonce).
   - Derives a 32-byte key and 12-byte nonce using Argon2id with secure parameters (4 iterations, 64MiB memory, 1 thread).
3. **Key Generation**: Uses ChaCha20IETF to generate a keystream of the specified size, written to `key.key` in 64KiB chunks.
4. **Output**: Creates or overwrites `key.key` with the binary keystream.

## Security Notes
- **Argon2id**: Uses Argon2id (memory-hard, resistant to GPU/brute-force attacks) for key and nonce derivation, making it suitable for cryptographic applications.
- **Deterministic Salts**: Salts are derived from the password for reproducibility (same password yields same `key.key`). For production, use random salts and store them with the output.
- **ChaCha20**: A secure stream cipher, safe for generating pseudorandom key material.
- **Recommendations**:
  - For production, use random salts (e.g., via `std.crypto.random.bytes`) and store them in a separate file or prepend to `key.key`.
  - Adjust Argon2 parameters (e.g., increase memory to 256MiB) for higher security, balancing performance.

## Building and Testing
1. Compile as above.
2. Test with:
   ```cmd
   keygen.exe 1024 mypassword
   ```
3. Verify `key.key`:
   - Check size: `dir key.key` (should be 1024 bytes).
   - Check contents: Use a hex editor or `xxd key.key` to inspect the binary output.
   - Run twice with the same args to confirm deterministic output.
4. Test errors:
   ```cmd
   keygen.exe
   keygen.exe 0 pass
   ```

## Limitations
- **Performance**: Direct file writes (no buffering) may be slower for very large files (e.g., 20GB). Argon2’s 64MiB memory usage may be intensive on low-memory systems.
- **Platform**: Tested on Windows x86_64. For other platforms, use appropriate Zig builds (e.g., Linux, macOS).
- **Determinism**: Deterministic salts are convenient for testing but less secure for production without random salts.

## Future Improvements
- Add random salt generation and storage for enhanced security.
- Implement buffered file writes for better performance on large files.
- Allow custom output file names via command-line arguments.
- Support configurable Argon2 parameters (iterations, memory) via arguments.

## License
Public domain. Use and modify freely.