In Haskell, the `main` "function" is actually a variable of type `IO ()`. This is a type that defines input/output behaviours; the compiler reduces the expressions in your program down to a single `IO ()`, which gives the resulting program.
For example:
```haskell
a = print "this"
b = print "that"

main = a
```
will print "this" to the console, but not "that".
You can chain IO behaviours together using the `>>` operator:
```haskell
main = (print "Hello, ") >> (print "world!")
```

We know `print` for output, but what about input? There is the function `getLine` that returns an `IO String`:
```haskell
main = getLine
```
This isn't very helpful, because we can't do anything with the result.
To actually use the result, we need the **bind operator** `>>=`. This operator allows us to feed the result of `getLine` into a function.
```haskell
main = getLine >>= (\n -> print n)
```
To be exact about the types:
```haskell
-- types of given functions
getLine :: IO String
main :: IO ()
(>>=) :: IO a -> (IO a -> IO b) -> IO b
-- that is, it takes an IO of one kind and a function that maps it to another, then returns the mapped value
-- in this case, the types are
-- (>>=) :: IO String -> (IO String -> IO ()) -> IO ()
print :: String -> IO () -- in this case, anyway

main = getLine >>= (\n -> print n)
```
A more complicated example of IO, using recursion to loop infinitely:
```haskell
main = putStrLn "Type \"foo\" rn:" >>
       getLine >>=
      (\n -> if n == "foo"
             then putStrLn "yeahhhhhh baby"
             else (putStrLn "that is not foo" >> main))
```
This syntax is verbose and ugly, even when you make it as tidy as possible with no brackets:
```haskell
main = putStrLn "Enter a name:" >>
       getLine >>=
       \n -> 
       putStrLn "Enter another name:" >>
       getLine >>=
       \m -> putStrLn ("Hello " ++ n ++ " and " ++ m)
```
Haskell has the `do` keyword which allows you to simplify it.
```haskell
main = do
    putStrLn "Enter a name:"
    n <- getLine
    putStrLn "Enter another name:"
    m <- getLine
    putStrLn ("Hello " ++ n ++ " and " ++ m)
```
