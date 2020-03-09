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
