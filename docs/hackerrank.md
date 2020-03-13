#### How to check a solution

#### Divisible Sum Pairs

[Link to the problem](https://www.hackerrank.com/challenges/divisible-sum-pairs/problem)

```idris
sol : Nat -> List Nat -> Nat
sol k xs =
  length [1 | i <- zip [0..length xs] xs,
              j <- zip [0..length xs] xs,
              fst i < fst j,
              (snd i + snd j) `mod` k == 0]

main : IO ()
main = do fline <- getLine
          sline <- getLine
          let k = the Nat (cast (snd (span (/= ' ') fline)))
          let arr = [ the Nat (cast v) | v <- words sline ]
          printLn (sol k arr)
```

**WARN**: `Solution is not total`
