1a. In cycles:
Option 1 avg instruction access time = $0.99 \cdot 1 + 0.01 \cdot 16 = 1.15$
Option 1 avg data access time = $0.9 \cdot 1 + 0.1 \cdot 16 = 2.5$
Option 1 avg memory time = $0.5 \cdot 1.15 + 0.5 \cdot (1.15+2.5) = 2.4$
Option 2 avg instruction/data access time = $0.98 \cdot 1 + 0.02 \cdot 16 = 1.3$
Option 2 avg memory time = $0.5 \cdot 1.3 + 0.5 \cdot (1.3 + 1.3) = 1.95$

Option 2 has the lowest average memory access time, with Option 1 having a 23% higher access time.

1b. Each address is 16 bits.
There are 32 lines, so the index will be log2(32) = 5 bits long.
Each line holds 32 addressable items, so the offset will be 5 bits long.
The tag will be the left over 6 bits.

1c. Assume order is tag\:index\:offset
i) tag = 010111, index = 11101, offset = 10101: miss
ii) tag = 010101, index = 10110, offset = 01010: miss
iii) tag = 110010, index = 01101, offset = 00000: miss
iv) tag = 010101, index = 10110,  offset = 11101: hit
v) tag = 111011, index = 10110, offset = 01001: miss

1d.
i) compulsory miss
ii) compulsory miss
iii) compulsory miss
iv) hit
v) conflict miss

1e. Hit rate of L1 = 0.98 (calculated from given values)
Desired hitrate = $3/1.5 = 2$
Let hit rate of L2 be $L2_{H}$.
Average memory access time = $1 + 0.02(10 + (1 - L2_H)100) = 2$ 
Solve for $L2_H$...
$L2_H = 0.6$
The level 2 cache must have a hit rate of 60% or greater.