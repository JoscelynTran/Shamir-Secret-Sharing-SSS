# Shamir's Secret Sharing Implementation

A Python implementation of Shamir's Secret Sharing Scheme (SSSS), a cryptographic algorithm that allows a secret to be divided into multiple shares, where a minimum threshold of shares is required to reconstruct the original secret.

## Overview

Shamir's Secret Sharing is based on polynomial interpolation over finite fields. The scheme provides:

- **Information-theoretic security**: Having fewer than the threshold number of shares reveals no information about the secret
- **Perfect reconstruction**: Any threshold number of shares can perfectly reconstruct the original secret
- **Flexibility**: Can create any (k,n) threshold scheme where k ≤ n

## Features

- ✅ Complete implementation of Shamir's (k,n) threshold scheme
- ✅ Uses cryptographically secure 256-bit prime field by default
- ✅ Efficient polynomial evaluation using Horner's method
- ✅ Modular inverse calculation using Extended Euclidean Algorithm
- ✅ Comprehensive error handling and validation
- ✅ Well-documented code with type hints

## Installation

### Requirements

- Python 3.6 or higher
- No external dependencies (uses only Python standard library)

### Getting Started

Simply copy the `shamir_secret_sharing.py` file to your project:

```bash
wget https://raw.githubusercontent.com/your-repo/shamir-secret-sharing/main/shamir_secret_sharing.py
```

## Usage

### Basic Example

```python
from shamir_secret_sharing import ShamirSecretSharing

# Create a (3,5) threshold scheme
# Need at least 3 out of 5 shares to recover the secret
sss = ShamirSecretSharing(k=3, n=5)

# Split a secret into shares
secret = 1234567890
shares = sss.split_secret(secret)

# Shares are (x,y) tuples
print(shares)
# Output: [(1, y1), (2, y2), (3, y3), (4, y4), (5, y5)]

# Reconstruct secret from any 3 shares
recovered = sss.reconstruct_secret(shares[:3])
print(recovered == secret)  # True
```

### Advanced Usage

```python
# Use custom prime for smaller finite field (for testing)
small_sss = ShamirSecretSharing(k=2, n=4, prime=97)

# Split a smaller secret
secret = 42
shares = small_sss.split_secret(secret)

# Reconstruct with different share combinations
subset1 = [shares[0], shares[2]]  # Use shares 1 and 3
subset2 = [shares[1], shares[3]]  # Use shares 2 and 4

recovered1 = small_sss.reconstruct_secret(subset1)
recovered2 = small_sss.reconstruct_secret(subset2)

print(recovered1 == secret)  # True
print(recovered2 == secret)  # True
```

## API Reference

### Class: `ShamirSecretSharing`

#### Constructor

```python
ShamirSecretSharing(k: int, n: int, prime: int = None)
```

Parameters:
- `k`: Threshold - minimum number of shares needed to reconstruct the secret
- `n`: Total number of shares to generate
- `prime`: (Optional) Prime number for the finite field. If not provided, uses a 256-bit prime

Raises:
- `ValueError`: If k > n, k < 2, or prime is not a prime number

#### Methods

##### `split_secret(secret: int) -> List[Tuple[int, int]]`

Splits a secret into n shares using polynomial interpolation.

Parameters:
- `secret`: The secret integer to be shared (must be 0 ≤ secret < prime)

Returns:
- List of n shares, each share is a tuple (x, y) where y = f(x)

Raises:
- `ValueError`: If secret is negative or ≥ prime

##### `reconstruct_secret(shares: List[Tuple[int, int]]) -> int`

Reconstructs the secret from k or more shares using Lagrange interpolation.

Parameters:
- `shares`: List of shares (at least k), each share is a tuple (x, y)

Returns:
- The reconstructed secret

Raises:
- `ValueError`: If fewer than k shares are provided

## How It Works

1. **Secret Splitting**:
   - Create a random polynomial f(x) of degree (k-1) where f(0) = secret
   - Evaluate the polynomial at n different points to create n shares
   - Each share is a point (x, f(x)) on the polynomial

2. **Secret Reconstruction**:
   - Take any k shares (points on the polynomial)
   - Use Lagrange interpolation to reconstruct the polynomial
   - Evaluate the polynomial at x=0 to recover the secret

## Security Considerations

- **Finite Field Size**: The default 256-bit prime provides strong security for most applications
- **Random Number Generation**: Uses Python's `random` module; for cryptographic applications, consider using `secrets` module
- **Share Distribution**: Shares should be distributed securely to participants
- **Share Storage**: Each participant should store their share securely
- **No Share Reuse**: Generate new shares for each secret; don't reuse polynomial coefficients

## Mathematical Background

The implementation is based on:
- Polynomial interpolation over finite fields (Zp)
- Lagrange interpolation formula
- Modular arithmetic
- Extended Euclidean Algorithm for modular inverse

For more details, see [Shamir's original paper](https://dl.acm.org/doi/10.1145/359168.359176).

## Examples

### Secure Password Sharing

```python
# CEO wants to share company master password
# Requires 3 out of 5 board members to recover
sss = ShamirSecretSharing(k=3, n=5)

# Convert password to integer (in practice, use proper encoding)
password_int = int.from_bytes("SuperSecretPass123!".encode(), 'big')
shares = sss.split_secret(password_int)

# Distribute shares to board members
for i, share in enumerate(shares):
    print(f"Board Member {i+1}: {share}")
```

### Multi-Signature Wallet

```python
# Cryptocurrency wallet requiring 2-of-3 signatures
sss = ShamirSecretSharing(k=2, n=3)

# Private key (simplified example)
private_key = 0x1234567890ABCDEF
shares = sss.split_secret(private_key)

# Any 2 people can sign transactions
recovered_key = sss.reconstruct_secret([shares[0], shares[2]])
print(hex(recovered_key))  # 0x1234567890abcdef
```

## Testing

Run the included test suite:

```python
python shamir_secret_sharing.py
```

This will demonstrate:
- Basic (3,5) threshold scheme
- Successful reconstruction with different share combinations
- Failure with insufficient shares

## Performance

- **Time Complexity**:
  - Splitting: O(k×n) for generating n shares
  - Reconstruction: O(k²) for k shares
- **Space Complexity**: O(n) for storing shares

## Limitations

- Secrets must be integers (0 ≤ secret < prime)
- For non-integer secrets, use appropriate encoding/decoding
- Share indices must be unique (typically 1 to n)

## Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Add tests for new functionality
4. Submit a pull request

## License

This implementation is provided for educational purposes. Please ensure compliance with your local regulations when using cryptographic software.

## References

- [Shamir, Adi (1979). "How to share a secret"](https://dl.acm.org/doi/10.1145/359168.359176)
- [Wikipedia: Shamir's Secret Sharing](https://en.wikipedia.org/wiki/Shamir%27s_Secret_Sharing)
- [Finite Field Arithmetic](https://en.wikipedia.org/wiki/Finite_field_arithmetic)

## Author

Your Name - Tuan Tran, ELTE

## Acknowledgments

Thanks to Adi Shamir for the original algorithm and the cryptographic community for continuous improvements and applications.
