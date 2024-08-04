https://ctftime.org/event/2423

# Conway (crypto)

Sequences... Sequences sequences... Sequences sequences sequences...

## Analysis

We are given the file `main.py`.

We can see that this code encrypts the flag with the result of hashing the return of `generate_next_key` for a given value.

`11131221131211131231121113112221121321132132211331222113112211` is an example of the integer sequence [A005150](https://oeis.org/A005150).

## Solution

1) Compute the next value in the sequence
2) Hash the next value in the sequence
3) Use the hash to decrypt the given ciphertext

```python
#!/usr/bin/env python3

import hashlib
from binascii import unhexlify
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad

def A005150(n):
    """from https://oeis.org/A005150"""
    p = "1"
    seq = [1]
    while (n > 1):
        q = ''
        idx = 0 # Index
        l = len(p) # Length
        while idx < l:
            start = idx
            idx = idx + 1
            while idx < l and p[idx] == p[start]:
                idx = idx + 1
            q = q + str(idx-start) + p[start]
        n, p = n - 1, q
        seq.append(int(p))
    return seq

initial = A005150(16)[-1]

h = hashlib.sha256()
h.update(str(initial).encode())
key = h.digest()

#print(key.hex()) # 609fd95c2155dfc76de2212c06b09f4ffa3b911d023b871f45a4eab530b393f3

ct = unhexlify("f143845f3c4d9ad024ac8f76592352127651ff4d8c35e48ca9337422a0d7f20ec0c2baf530695c150efff20bbc17ca4c")

cipher = AES.new(key, AES.MODE_ECB)
flag = unpad(cipher.decrypt(ct), 16).decode()

print(flag) # TFCCTF{c0nway's_g4me_0f_sequences?}
```

## Flag
`TFCCTF{c0nway's_g4me_0f_sequences?}`

smiley 2024/08/03