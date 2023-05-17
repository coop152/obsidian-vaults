Functions must be exhaustive
Avoid actual errors, use Maybe instead, e.g:
```haskell
-- wont compile, not exhaustive
badHead :: [a] -> a
-- cant define a base case, what would the value be?
-- badHead [] = ???
badHead (x : xs) = x

main = print (badhead []) -- undefined

safeHead :: [a] -> Maybe a
safeHead [] = Nothing  -- safe for all inputs
safeHead (x : xs) = Just x

main = print (badhead []) -- defined, outputs Nothing
```

## Strictness of Functions
A function is strict in some part of its arguments if an error value in that part will cause the function to return an error value. For example:
```haskell
f x = x
```
`f` is strict in all of its arguments.
```haskell
g x = 5
```
`g` is not strict in its arguments.
```haskell
head :: [a] -> Maybe a
head [] = Nothing
head (x:xs) = Maybe x
```
`head` is strict in the argument's head, but not in its tail. It is also strict in the argument itself, as if an error was passed instead of a list it would fail to destructure it.
```haskell
sum :: [Integer] -> Integer
sum [] = 0
sum (x:xs) = x + sum xs
```
`sum` is strict in the argument, it's head, and its tail. In addition it is also strict in its "spine"; that is, the shape of the object must be correct. In this case, if the list was `1 : 2 : errVal` then `sum` would also fail.

Consider the logical AND operator: `x && y`
Because this operator short circuits, it is only strict on **it's first argument**. In some cases the second argument will be evaluated, but not all.

```haskell
len :: [Integer] -> Integer
len [] = 0
len (x : xs) = 1 + len xs
```
`len` is strict in the spine of its argument, but not in the head or tail.

## Infinite Values
Using recursion, you can define infinite values:
```haskell
naturals = 0:[n + 1 | n <- naturals]
```
This generates the natural numbers by repeatedly increasing every number in the list by one and adding a new 0 to the start.
You can take some numbers from an infinite list using the `take` function:
```haskell
main = print (take 6 naturals)
```
Or use the index operator to take one (equivalent to square braces in other languages):
```haskell
main = print (naturals 45)
```

Here is the list of Fibonacci numbers:
```haskell
fib = 1:1:[fib !! n + fib !! (n + 1) | n <- [0..]]
```

You can have infinite lists like this due to Haskell's lazy evaluation; the list is only populated up to the point you access. Of course, if you try summing an infinite list then nothing will happen.

You can define the primes using a filter on an infinite list:
```haskell
sieve [] = []
sieve (x:xs) = x:[v | v <- xs, v `mod` x /= 0]

primes = sieve [2..]
```