#### How to check a solution

#### Divisible Sum Pairs

[Link to the problem](https://www.hackerrank.com/challenges/divisible-sum-pairs/problem)

```idris
import Data.Vect
import Data.String

sol : Nat -> List Nat -> Nat
sol k xs =
  length [1 | i <- zip [0..length xs] xs,
              j <- zip [0..length xs] xs,
              fst i < fst j,
              (snd i + snd j) `mod` k == 0]

castToNat : Maybe Integer -> Nat
castToNat i = case i of
                Nothing => 0
                Just int => cast int

main : IO ()
main = do fline <- getLine
          sline <- getLine
          let k = castToNat (parsePositive (snd (span (/= ' ') fline)))
          let arr = map castToNat (map parsePositive (words sline))
          putStrLn (show (sol k arr))
```
