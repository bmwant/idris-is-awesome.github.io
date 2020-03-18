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
