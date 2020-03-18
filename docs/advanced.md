#### Quick sort

```idris
quicksort : Ord elem => List elem -> List elem
quicksort [] = []
quicksort (x :: xs) =
  let lesser = filter (< x) xs
      greater = filter(>= x) xs in
        (quicksort lesser) ++ [x] ++ (quicksort greater)
```

**WARN**: `function is not total`

#### Insertion sort

```idris
import Data.Vect

insert : Ord elem =>
         (x : elem) -> (xsSorted : Vect len elem) -> Vect (S len) elem
insert x [] = [x]
insert x (y :: xs) = case x < y of
                          True => x :: y :: xs
                          False => y :: insert x xs

insSort : Ord elem => Vect n elem -> Vect n elem
insSort [] = []
insSort (x :: xs) = let xsSorted = insSort xs in
                        insert x xsSorted
```

#### Transpose a matrix

This is the implementation similar to the [one from Data.Vect](https://github.com/idris-lang/Idris-dev/blob/master/libs/base/Data/Vect.idr)

```idris
import Data.Vect

transposeMat : Vect m (Vect n elem) -> Vect n (Vect m elem)
transposeMat [] = replicate _ []
transposeMat (x :: xs) = let xsTrans = transposeMat xs in
                         zipWith (::) x xsTrans
```

Slightly more verbose version

```idris
import Data.Vect

createEmpties : Vect n (Vect 0 elem)
createEmpties = replicate _ []

transposeHelper : (x : Vect n elem) ->
                  (xsTrans : Vect n (Vect k elem)) ->
                  Vect n (Vect (S k) elem)
transposeHelper [] [] = []
transposeHelper (x :: xs) (y :: ys) = (x :: y) :: transposeHelper xs ys

transposeMat : Vect m (Vect n elem) -> Vect n (Vect m elem)
transposeMat [] = createEmpties
transposeMat (x :: xs) = let xsTrans = transposeMat xs in
                         transposeHelper x xsTrans
```

#### Multiply matrices

```idris
import Data.Vect

dotProduct : Num elem =>
             Vect n elem -> Vect n elem -> elem
dotProduct [] [] = 0
dotProduct (x :: xs) (y :: ys) = x*y + dotProduct xs ys

multiplyMat : Num elem =>
              Vect n (Vect m elem) ->
              Vect m (Vect p elem) ->
              Vect n (Vect p elem)
multiplyMat [] _ = []
multiplyMat (x :: xs) ys = let ysTrans = transpose ys in
                           map (dotProduct x) ysTrans :: multiplyMat xs ys
```

* Separate declaration for a `Matrix` type

```idris
import Data.Vect

Matrix : Nat -> Nat -> Type -> Type
Matrix n m elem = Vect n (Vect m elem)

multiplyMat : Num t => Matrix n m t -> Matrix m p t -> Matrix n p t
multiplyMat x y = multiplyMat' x (transpose y)
  where
        dot : Num t => Vect n t -> Vect n t -> t
        dot xs ys = sum $ zipWith (*) xs ys

        multiplyMat' : Num t => Matrix n m t -> Matrix p m t -> Matrix n p t
        multiplyMat' (x :: xs) ys = map (dot x) ys :: multiplyMat' xs ys
        multiplyMat' [] _ = []
```
