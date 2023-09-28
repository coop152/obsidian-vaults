1a. $[(xb)x]$
$\lambda y.(\lambda{x}.[(xb)x])$
$\lambda w.(w\lambda x.[\lambda y.(\lambda{x}.[(xb)x])])$

1b. 
(1) $xb$    scApp with $x$ and $b$
(2) $(xb)x$    scApp with (1) and $x$
(3) $\lambda{x}.[(xb)x]$    scAbs with $x$ and (2)
(4) $\lambda y.(\lambda{x}.[(xb)x])$    scAbs with $y$ and (3)
(5) $\lambda x.[\lambda y.(\lambda{x}.[(xb)x])]$    scAbs with $x$ and (4)
(6) $w\lambda x.[\lambda y.(\lambda{x}.[(xb)x])]$    scApp with $w$ and (5)
(7) $\lambda w.(w\lambda x.[\lambda y.(\lambda{x}.[(xb)x])])$    scAbs with $w$ and (6)

1c. $[(xb)x]$
Free = {x, b}, Bound = {}
$\lambda y.(\lambda{x}.[(xb)x])$
Free = {b}, Bound = {x, y}
$\lambda w.(w\lambda x.[\lambda y.(\lambda{x}.[(xb)x])])$
Free = {b}, Bound = {w, x, y}

1d. 
Given: $\lambda w.(w\lambda x.[\lambda y.(\lambda{x}.[(xb)x])])$
$\alpha$-equivalent: $\lambda c.(c\lambda a.[\lambda b.(\lambda{a}.[(ab)a])])$ 

