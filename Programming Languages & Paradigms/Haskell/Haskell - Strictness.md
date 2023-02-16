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