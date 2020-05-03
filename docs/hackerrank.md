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
sol Z _ = 0
sol (S k) xs =
  length [1 | i <- zip [0..length xs] xs,
              j <- zip [0..length xs] xs,
              fst i < fst j,
              modNatNZ (snd i + snd j) (S k) SIsNotZ == 0]

main : IO ()
main = do fline <- getLine
          sline <- getLine
          let k = the Nat (cast (snd (span (/= ' ') fline)))
          let arr = [ the Nat (cast v) | v <- words sline ]
          printLn (sol k arr)
```

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

#### Mini-Max Sum

[Link to the problem](https://www.hackerrank.com/challenges/mini-max-sum/problem)

```idris
import Data.String

||| Returns end of the list
end : List Integer -> List Integer
end [] = []
end (x :: xs) = xs

||| Returns start of the list
start : List Integer -> List Integer
start [] = []
start lst = st lst [] where
  st : List Integer -> List Integer -> List Integer
  st [] current = current
  st [x] current = current
  st (x :: xs) current = x :: (st xs current)

main : IO ()
main = do input <- getLine
          let arr = catMaybes [ parseInteger n | n <- words input ]
          let minSum = sum $ start $ sort arr
          let maxSum = sum $ end $ sort arr
          putStrLn (show minSum ++ " " ++ show maxSum)
```

#### Birthday Chocolate

[Link to the problem](https://www.hackerrank.com/challenges/the-birthday-bar/problem)

```idris
count : Eq elem => elem -> List elem -> Nat
count e [] = 0
count e (x :: xs) = (if e == x then 1 else 0) + count e xs

slices : List Nat -> Nat -> List (List Nat)
slices [] _ = []
slices (x :: xs) n = let slice = take n (x :: xs) in
  if length xs >= n
  then slice :: slices xs n
  else [slice]

sol : List Nat -> Nat -> Nat -> Nat
sol [] _ _ = 0
sol lst d m = count d (map sum (slices lst m))

main : IO ()
main = do fline <- getLine
          sline <- getLine
          tline <- getLine

          let bar = [ the Nat (cast v) | v <- words sline ]
          let d = the Nat (cast (fst (span (/= ' ') tline)))
          let m = the Nat (cast (snd (span (/= ' ') tline)))
          printLn (sol bar d m)
```

#### Cats and a Mouse

[Link to the problem](https://www.hackerrank.com/challenges/cats-and-a-mouse/problem)

```idris
triples : List Nat -> (Nat, Nat, Nat)
triples (x :: y :: z :: rest) = (x, y, z)
triples _ = (0, 0, 0)

sol : Integer -> Integer -> Integer -> String
sol x y z = case abs(x-z) `compare` abs(y-z) of
              LT => "Cat A"
              EQ => "Mouse C"
              GT => "Cat B"

getInput : Nat -> IO (List (List Nat))
getInput Z = pure []
getInput (S k) =
  do line <- getLine
     let values = [ the Nat (cast v) | v <- words line ]
     rest <- getInput k
     pure ([values] ++ rest)

solQueries : (Nat, Nat, Nat) -> String
solQueries (x, y, z) = sol (cast x) (cast y) (cast z)

main : IO ()
main = do fline <- getLine
          let n = the Nat (cast fline)
          input <- getInput n
          let queries = map triples input
          let result = map solQueries queries
          putStr $ unlines result
```

#### Picking Numbers

[Link to the problem](https://www.hackerrank.com/challenges/picking-numbers/problem)

```idris
count : Eq elem => elem -> List elem -> Nat
count e [] = 0
count e (x :: xs) = (if e == x then 1 else 0) + count e xs

longest : List Nat -> List Nat -> Nat
longest [] lst = 0
longest (x :: xs) lst =
  max (check x) (longest xs lst) where
    check : Nat -> Nat
    check Z = (count Z lst) + (count (S Z) lst)
    check (S k) =
      let left = (count k lst) + (count (S k) lst)
          right = (count (S k) lst) + (count (S (S k)) lst) in
          max left right

sol : List Nat -> Nat
sol [] = 0
sol lst = let unique = nub lst in
              longest unique lst

main : IO ()
main = do _ <- getLine
          line <- getLine
          let arr = [ the Nat (cast v) | v <- words line ]
          printLn $ sol arr
```

#### Utopian Tree

[Link to the problem](https://www.hackerrank.com/challenges/utopian-tree/problem)

```idris
getInput : Nat -> IO (List Nat)
getInput Z = pure []
getInput (S k) =
  do line <- getLine
     let value = the Nat (cast line)
     rest <- getInput k
     pure (value :: rest)

sol : Nat -> Nat
sol Z = 1
sol (S k) = case modNatNZ (S k) 2 SIsNotZ of
              Z => sol k + 1 -- summer
              _ => sol k * 2 -- spring

main : IO ()
main = do line <- getLine
          let n = the Nat (cast line)
          input <- getInput n
          let result = map sol input
          putStr $ unlines (map (\x => the String (cast x)) result)
```

#### Viral Advertising

[Link to the problem](https://www.hackerrank.com/challenges/strange-advertising/problem)

```idris
shared : Nat -> Nat
shared Z = 5
shared (S k) = (divNatNZ (shared k) 2 SIsNotZ) * 3

liked : Nat -> Nat
liked Z = 0
liked (S k) = (divNatNZ (shared k) 2 SIsNotZ) + liked k

main : IO ()
main = do line <- getLine
          printLn $ liked $ the Nat $ cast line
```

#### Staircase

[Link to the problem](https://www.hackerrank.com/challenges/staircase/problem)

```idris
repeat : Nat -> Char -> IO ()
repeat Z _ = pure ()
repeat (S k) c = putChar c >>= \_ => repeat k c

printLine : (k : Nat) -> (n : Nat) -> {auto smaller : LTE k n} -> IO ()
printLine Z _ = pure ()
printLine k n = do repeat (n-k) ' '
                   repeat k '#'
                   putStrLn ""

-- k - hashes
-- n - row length
sol : Nat -> Nat -> IO ()
sol k n =
  case isLTE k n of
    Yes prf => do printLine k n
                  sol (k+1) n
    No contra => pure ()

main : IO ()
main = do line <- getLine
          let n = the Nat (cast line)
          sol 1 n
```

#### Queen's Attack II

[Link to the problem](https://www.hackerrank.com/challenges/queens-attack-2/problem)

```idris
getPair : IO (Nat, Nat)
getPair = do line <- getLine
             let (num1, num2) = span (/= ' ') line
             pure (the Nat (cast num1), the Nat (cast num2))

getInput : Nat -> IO (List (Nat, Nat))
getInput Z = pure []
getInput (S k) =
  do (x, y) <- getPair
     rest <- getInput k
     pure ((x, y) :: rest)

||| Takes initial position, size of a board and list of obstacles
||| Returns number of squares it can reach
count : (Nat, Nat) -> Nat -> List (Nat, Nat) -> Nat
count (x0, y0) k lst = sum [
  line (x0, y0) ( 0,  1), -- move up
  line (x0, y0) ( 0, -1), -- move down
  line (x0, y0) ( 1,  0), -- move right
  line (x0, y0) (-1,  0), -- move left
  line (x0, y0) ( 1,  1), -- top right
  line (x0, y0) (-1,  1), -- top left
  line (x0, y0) ( 1, -1), -- bottom right
  line (x0, y0) (-1, -1)  -- bottom left
] where
  line : (Nat, Nat) -> (Integer, Integer) -> Nat
  line (x0', y0') (dx, dy) =
    let newX = cast x0' + dx
        newY = cast y0' + dy in
        if newX > 0 && newX <= cast k &&
           newY > 0 && newY <= cast k &&
           not (elem (cast newX, cast newY) lst)
        then 1 + line (cast newX, cast newY) (dx, dy)
        else 0

main : IO ()
main = do (n, k) <- getPair
          (x0, y0) <- getPair
          obstacles <- getInput k
          printLn $ count (x0, y0) n obstacles
```
