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

#### Reading from a file

```idris
main : IO ()
main = do
  (Right content) <- readFile "input.txt" | (Left err) => printLn err
  putStrLn content
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
