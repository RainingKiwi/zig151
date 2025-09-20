Keygen: A ChaCha20 Key Generator
Overview
keygen is a command-line tool written in Zig that generates a binary key file (key.key) of a specified size using the ChaCha20 stream cipher. The key is derived deterministically from a user-provided password using SHA-256 hashing, making it suitable for reproducible key generation in non-production environments.
Requirements

Zig Compiler: Version 0.15.1 (stable) for Windows x86_64. Download from ziglang.org.
Windows: Tested on Windows 10/11 (x86_64 architecture).
Disk Space: Enough to store the output file (up to 20GB).

Installation

Download and extract zig-x86_64-windows-0.15.1.zip to a directory, e.g., C:\Users\<YourUsername>\OneDrive\Desktop\zig.
Place keygen.zig in a project directory, e.g., C:\Users\<YourUsername>\OneDrive\Desktop\zig1.
Ensure zig.exe is accessible (either add to PATH or use full path).

Usage
Compile and run the tool from the command prompt in your project directory.
Compile
C:\Users\<YourUsername>\OneDrive\Desktop\zig\zig.exe build-exe keygen.zig

Run
keygen.exe <size_bytes> <password>


<size_bytes>: Size of the output file in bytes (1 to 20GB).
<password>: Password to derive the key and nonce.

Example:
keygen.exe 1024 mypassword

Creates a 1024-byte key.key file in the current directory.
Error Handling

If <size_bytes> is invalid (<1 or >20GB), prints: Error: size must be between 1 byte and 20GB.
If arguments are missing, prints: Usage: keygen <size_bytes> <password>.

How It Works

Input Parsing: Reads <size_bytes> and <password> from command-line arguments.
Key Derivation:
Computes SHA-256 of the password to derive a 32-byte key.
Computes SHA-256 of the password + "keygen-nonce" to derive a 12-byte nonce.


Key Generation: Uses ChaCha20IETF to generate a keystream of the specified size, written to key.key in 64KiB chunks.
Output: Creates or overwrites key.key with the binary keystream.

Security Notes

SHA-256 Limitation: The tool uses SHA-256 for key derivation, which is fast but vulnerable to brute-force attacks. For production use, consider replacing with Argon2 (std.crypto.pwhash.argon2) for stronger password-based key derivation.
Deterministic Output: The same password and size produce the same key.key, useful for testing but not secure for cryptographic keys without a random salt.
Recommendations: For secure applications, use random salts and Argon2, and store salts alongside the key file.

Building and Testing

Compile as above.
Test with:keygen.exe 1024 mypassword


Verify key.key:
Check size: dir key.key (should be 1024 bytes).
Check contents: Use a hex editor or xxd key.key to inspect the binary output.
Run twice with the same args to confirm deterministic output.


Test errors:keygen.exe
keygen.exe 0 pass



Limitations

Performance: Direct file writes (no buffering) may be slower for very large files (e.g., 20GB). Consider adding buffering for optimization.
Platform: Tested on Windows x86_64. For other platforms, use appropriate Zig builds (e.g., Linux, macOS).
Security: Not suitable for production cryptographic use without upgrading to Argon2.

Future Improvements

Switch to std.crypto.pwhash.argon2 for secure key derivation.
Add random salt generation and storage for non-deterministic keys.
Implement buffering for file writes to improve performance.
Add output file name as a command-line argument.

License
Public domain. Use and modify freely.