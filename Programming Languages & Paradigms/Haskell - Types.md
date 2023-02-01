- Checked at compile time
- Every variable always has a type
#### Example: Giving the type of a function
```haskell
f :: Int -> Int
f x = x + 1  -- for example

big :: Int -> Bool
big x = x > 10
```
#### Example: Giving the type of a literal
```haskell
-- using f from the previous example
main = print (f 5)  -- infers the type of 5 as Int

main = print (f 5::Int)  -- explicitly gives the type of 5 as Int

main = print (f 5::Double)  -- explicity gives the type of 5 as Double; will not compile!
```

The types `Int` and `Double` are the typical fixed-size types you would expect from other languages. There also exists `Integer`, which has unbounded size.

## Generic Types
Haskell has type variables that allow generic functions to be made:
```haskell
j :: a -> Bool  -- a is a type variable; this function can take any input
j x = True

-- all compile and work properly
main = print (j 5)
main = print (j True)
main = print (j 2.5)
```
Using type constructors, more complicated types can be made:
```haskell
sumPairs :: (Int, Int) -> Int
sumPairs (x, y) = x + y
```
The type constructor of a function is an arrow, as has already been shown in previous examples:
```haskell
applyToTen :: (Int -> Bool) -> Bool
applyToTen f = f(10)

main = print (\x -> True) -- also featured here; anonymous function syntax
```
using multiple arguments and type variables:
```haskell
applyFunc :: (a -> b) -> a -> b
applyFunc f x = f x

main = print (applyFunc (\x -> 2 * x) 5)
```

## Algebraic Data Types
Make custom types using the `Data` keyword. The simplest example is an enum:
```haskell
data SwitchState = On | Off  -- an enum for a switch

isOn :: SwitchState -> Bool
isOn On = True
isOn Off = False

toggle :: SwitchState -> SwitchState
toggle On = Off
toggle Off = On
```
Type constructors using other types can be made:
```haskell
-- creates a type constructor IntPair with two Int arguments
data MyIntPair = IntPair Int Int  

sumIntPair :: MyIntPair -> Int
sumIntPair (IntPair a b) = a + b

main = print (sumIntPair (IntPair 10 20))  -- using constructor to make a new MyIntPair
```
