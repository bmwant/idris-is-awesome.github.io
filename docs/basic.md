#### Compiling and running a program

```bash
$ idris hello.idr -o hello
$ ./hello
```

Using Idris REPL

```bash
$ idris hello.idr
*hello> :c hello
*hello> :q
$ ./hello
```

#### Reading input from user

* Show length of the input string

```idris
main : IO ()
main = do
  putStr "Enter a string: "
  x <- getLine
  putStrLn ("Length is " ++ show (length x))
```

* Show sum of the numbers from input

```idris
import Data.String

sumNums : List (Maybe Integer) -> Maybe Integer
sumNums [] = Just 0
sumNums (x :: xs) = case (x, sumNums xs) of
                      (Just num1, Just num2) => Just(num1 + num2)
                      (_, _) => Nothing
main : IO ()
main = do
  putStr "Enter numbers separated by a space: "
  x <- getLine
  case sumNums (map parseInteger (words x)) of
    Just sum => putStrLn ("Sum is " ++ show sum)
    Nothing => putStrLn "Invalid input"
```

#### Reading from a file

```idris
main : IO ()
main = do
  (Right content) <- readFile "input.txt" | (Left err) => printLn err
  putStrLn content
```

* Using `case`

```idris
main : IO ()
main = do file <- readFile "input.txt"
          case file of
            Right content => putStrLn content
            Left err => printLn err
```

#### Writing to a file

```idris
main : IO ()
main = do res <- writeFile "output.txt" "Test file content\n"
          printLn res
```

#### Pattern match a value of expression

* Using `with` (accepts predicate and a list of elements and returns list of those elements for which predicate is true)

```idris
my_filter : (elem -> Bool) -> List elem -> List elem
my_filter f [] = []
my_filter f (x :: xs) with (f x)
  my_filter f (x :: xs) | True = x :: (my_filter f xs)
  my_filter f (_ :: xs) | False = my_filter f xs
```

```idris
my_filter isDigit ['1', 'a', '2', 'b']
['1', '2']
```

#### Is odd/even number

```idris
mutual
  isEven : Nat -> Bool
  isEven Z = True
  isEven (S k) = isOdd k

  isOdd : Nat -> Bool
  isOdd Z = False
  isOdd (S k) = isEven k
```


#### List comprehensions

* [Pythagorean triples](https://en.wikipedia.org/wiki/Pythagorean_triple) in a given range

```idris
pythag: Int -> List (Int, Int, Int)
pythag n = [ (x, y, z) | z <- [1..n], y <- [1..z], x <- [1..y],
                         x*x + y*y == z*z ]
```

* Count word lengths in a sentence

```idris
wordLengths: String -> List Nat
wordLengths string = [ length word | word <- words string ]
```

* List of squares in a given range which are only even

```idris
evenSquares: Nat -> List Nat
evenSquares n = [ x*x | x <- [1..n], x*x `mod` 2 == 0 ]
```

* Define and use variable within list comprehension

```idris
someCubes: Nat -> List Nat
someCubes n = [ y | x <- [1..n], let y = x*x*x, y `mod` 5 /= 0 ]
```

#### Interfaces and implementations

* Implement equality check for your type

```idris
data Person : Type where
  MkPerson : String -> Int -> Person

implementation Eq Person where
  (MkPerson name1 age1) == (MkPerson name2 age2) =
    (name1 == name2) && (age1 == age2)

alice : Person
alice = MkPerson "Alice" 24

bob : Person
bob = MkPerson "Bob" 42
```

```idris
alice /= bob
True : Bool

alice == bob
False : Bool
```

### Generate random number

```idris
-- tbd
```

### Convert binary number to decimal

```idris
data BinChar : Char -> Type where
  O : BinChar '0'
  I : BinChar '1'

data Every : (a -> Type) -> List a -> Type where
  Nil : {P : a -> Type} -> Every P []
  (::) : {P : a -> Type} -> P x -> Every P xs -> Every P (x :: xs)
  
fromBinChars : Every BinChar xs -> Nat -> Nat
fromBinChars [] _ = 0
fromBinChars (_ :: ys) Z = 1 
fromBinChars (O :: ys) (S k) = fromBinChars ys k
fromBinChars (I :: ys) (S k) = pow 2 k + fromBinChars ys k

b : (s : String) -> {auto p : Every BinChar (unpack s)} -> Nat
b {p} s = fromBinChars p (length s)
```

Example of usage

```idris
Idris> b"10110"
22 : Nat
```

which under the hood will call `fromBinChars [I, O, I, I, O] 5`
