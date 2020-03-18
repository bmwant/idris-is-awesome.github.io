#### How to check a solution

Compile your code to any target language [supported by Hackerrank](https://www.hackerrank.com/environment)

Example for [Node.js](https://nodejs.org/en/) below

```bash
$ idris --codegen node solution.idr -o solution.js
```

Due to filesize limitation of `50 KB` you need to minify your code.
Install [UglifyJS](https://github.com/mishoo/UglifyJS2) with support for ES6

```bash
$ npm install -g "uglify-js@github:mishoo/UglifyJS2#harmony"
```

Launch compression with optional `--compress "unsafe=true"` flag

```bash
$ uglifyjs solution.js --compress --mangle > solution.min.js
```

Check size of the resulting file with [fsize](https://unix.stackexchange.com/a/572598/196787)

```bash
$ fsize solution.min.js
25.86 KB
```

Now you are good to upload your solution!

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

**WARN**: `solution is not total`


#### Migratory Birds

[Link to the problem](https://www.hackerrank.com/challenges/migratory-birds/problem)

```idris
import Data.String


count : Eq elem => elem -> List elem -> Nat
count e [] = 0
count e (x :: xs) = (if e == x then 1 else 0) + count e xs

||| For each element in a list count number of
||| its entries within a list
entriesCount : Eq elem => List elem -> List Nat
entriesCount lst = map (\x => count x lst) lst

||| Returns biggest element in a list
maxElem : Ord elem => List elem -> Maybe elem
maxElem [] = Nothing
maxElem (x :: xs) = case maxElem xs of
                      Nothing => Just x
                      Just maxRest => Just (max x maxRest)

||| Returns index of the biggest element
maxElemIndex : Ord elem => List elem -> Maybe Nat
maxElemIndex lst = let maxEntry = maxElem lst in
                       case maxEntry of
                         Nothing => Nothing
                         Just m => elemIndex m lst

||| Returns index of the biggest element
sol : (Ord elem, Eq elem) => List elem -> Maybe elem
sol lst = let sorted = sort lst
              ind = maxElemIndex $ entriesCount sorted in
              case ind of
                Nothing => Nothing
                Just n => index' n sorted

main : IO ()
main = do _ <- getLine
          sline <- getLine
          let arr = catMaybes [ parseInteger n | n <- words sline ]
          case sol arr of
            Nothing => pure ()
            Just result => printLn result
```
