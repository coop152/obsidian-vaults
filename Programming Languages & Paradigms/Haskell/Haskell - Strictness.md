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