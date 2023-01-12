**GPU**s can perform a limited set of specific instructions, but on large amounts of data in parallel. This is suited for graphics rendering, where lots of unrelated, identical mathematical operations are being done en masse. This parallelism gives GPUs high throughput.

**CPU**s can perform a large set of varied instructions, but only on limited amounts of data at a time and in a serial manner. This is suited for general computing, where programs can be varied and each operation may or may not rely heavily on others. Due to this, CPUs have low latency to perform these serial operations quickly but lower throughput due to the low amount of parallelism.

### FPUs
#### Floating point representation (IEEE 754)
![[Pasted image 20230111150459.png]]
#### Conversion procedure
[[https://www.youtube.com/watch?v=8afbTaA-gOQ]]
1. Convert the number to fixed point binary, ignoring the sign. The sign bit is 1 if the number was negative, and 0 if the number was positive.
2. Normalise the number (i.e. move the point to be as far left as possible, with a 1 on the left of the point.). Remove the leading 1 from the normalised number's mantissa and you have the FP mantissa.
3. Take the normalised number's exponent (e.g. $2^6$). if the number in the power is positive, the FP exponent is (for example) $127 + 6$. If the number in the power is positive (e.g. $2^{-6}$), the FP exponent is (for example) $127 - 6$.
4. Pad the numbers to fit the format.

### DSPs
