title: F♯ Simple Simple
author:
  name: Yong Wen Chua
  twitter: lawlietsg

output: one.html
controls: true
style: style.css

--

# F♯ Simple Simple
## A simple first look at F♯

--

### Why F♯ and not Haskell?

 - Wanted something that has a robust ecosystem of libraries
 - With existing tools like testing, linting, build etc.

### Why F♯ and not Scala/Clojure?

 - More familiar with .NET ecosystem
 - Also... [.NET CORE](https://github.com/dotnet/core)!
--

### What is F♯?

 - F♯ is a language in the .NET family
 - Compiles to run code on the 
 [Common Language Runtime](https://github.com/dotnet/coreclr) with support of F♯ 
 specific libraries
 - Strongly and statically typed
 - Type system is common to CLR -- single-inheritance, multiple interface and 
 generics
 - Compiler tries to infer as much as possible
 - Multi paradigm, but focused on functional 
 - Encourages the use of immutable data structures. Makes it more difficult
 to mutate data

--
### Hello World

```fsharp
> System.Console.WriteLine "Hello world";;
Hello world
val it : unit = ()

```
### Values
 - Immutable values are bound (not assigned) with `let

```fsharp
let x = 5
let y = 10
let z = x + y;;

val x : int = 5
val y : int = 10
val z : int = 15
```
--
### Functions
 - Are also values
 - Final statement executed is the return value

```fsharp
let add x y = x + y  
let sum = add x 6;;

val add : x:int -> y:int -> int
val sum : int = 11
```
--
### Some more complicated calls


```fsharp
let add x y = x + y
 
 let sub x y = x - y
 
 let printThreeNumbers num1 num2 num3 =
     printfn "num1: %i" num1
     printfn "num2: %i" num2
     printfn "num3: %i" num3
 
printThreeNumbers 5 (add 10 7) (sub 20 8);;

num1: 5
num2: 17
num3: 12

val add : x:int -> y:int -> int
val sub : x:int -> y:int -> int
val printThreeNumbers : num1:int -> num2:int -> num3:int -> unit
val it : unit = ()
``` 
--
### Recursion

```fsharp
let rec fib = function
     | 0 -> 0
     | 1 -> 1
     | n -> fib (n - 1) + fib (n - 2)  
fib 10;;

val fib : _arg1:int -> int
val it : int = 55
```

###Stack overflow (and really slow)
```fsharp
fib 100;;
```

--
### Another example
```fsharp
let rec factorial = function
    | n when n = 0I -> 1I
    | n -> n * factorial (n - 1I)

factorial 100I
factorial 100000I
```
--
### Tail Recursion


```fsharp
// Tail recursive Fibonacci
// BigInt -> BigInt
let bigFib n =
    let rec loop previous previousPrevious = function
        | n when n = 0I -> previousPrevious
        | n -> loop (previous + previousPrevious) previous (n - 1I)
    loop 1I 0I n

val bigFib : n:System.Numerics.BigInteger -> System.Numerics.BigInteger
```

### Some demonstrations
```fsharp
[0I .. 100I] |> List.iter (fun x -> printfn "fib(%O) = %O" x (bigFib x))

[0I .. 100I .. 10000I] |> List.iter (fun x -> printfn "fib(%O) = %O" x (bigFib x))
```

--

### Another one
```fsharp
let factorial n = 
    let rec loop acc = function
        | n when n = 0I -> acc
        | n -> loop (acc * n) (n - 1I)
    loop 1I n

factorial 100000I
```


```fsharp
let factorial = function
    | n when n = 0I -> 1I
    | n -> seq { 1I..n } |> Seq.fold (fun x product -> x * product) 1I

factorial 100000I
```

--
### Reading types

 - `val function : domain -> range`

Consider
```fsharp
> let addAndMakeString x y = (x + y).ToString();;
val addAndMakeString : x:int -> y:int -> string
```
--

### Function currying

```fsharp
let addTwoNumbers x y = x + y   
let addFive = addTwoNumbers 5.0f
let sum = addFive 4.0f;;        

val addTwoNumbers : x:float32 -> y:float32 -> float32
val addFive : (float32 -> float32)
val sum : float32 = 9.0f
```

So, back to `addAndMakeString`

```fsharp
let addAndMakeString x = 
    let intermediate y = (x + y).ToString()
    intermediate

val addAndMakeString : x:int -> (int -> string)
```
--

### Applications of partial applications
[Credits](https://fsharpforfunandprofit.com/posts/partial-application/)

```fsharp
// create an "adder" by partial application of add
let add42 = (+) 42    // partial application
add42 1
add42 3

// create a new list by applying the add42 function 
// to each element
[1;2;3] |> List.map add42 

// create a "tester" by partial application of "less than"
let twoIsLessThan = (<) 2   // partial application
twoIsLessThan 1
twoIsLessThan 3

// filter each element with the twoIsLessThan function
[1;2;3] |> List.filter twoIsLessThan 

// create a "printer" by partial application of printfn
let printer = printfn "printing param=%i" 

// loop over each element and call the printer function
[1;2;3] |> List.iter printer   

printing param=1
printing param=2
printing param=3

val add42 : (int -> int)
val twoIsLessThan : (int -> bool)
val printer : (int -> unit)
val it : unit = ()

```
--
### Piping
Consider the pipe function defined as:
```fsharp
let (|>) x f = f x

let doSomething x y z = x+y+z
doSomething 1 2 3       // all parameters after function

let doSomethingPartial = doSomething 1 2
doSomethingPartial 3     // only one parameter after function now
3 |> doSomethingPartial  // same as above - last parameter piped in

"12" |> int               // parses string "12" to an int
1 |> (+) 2 |> (*) 3       // chain of arithmetic
```
### Reverse Pipe
```fsharp
let (<|) f x = f x

printf "%i" <| 1+2       // using reverse pipe
```
--
### Together now
```fsharp
let add x y = x + y
(1+2) add (3+4)          // error
1+2 |> add <| 3+4        // pseudo infix
```
--
### Sudoku Solver
[Full source code](https://github.com/lawliet89/fsudoku/blob/master/fsudoku/Program.fs)

```
$./fsudoku.exe fsudoku/difficult.txt 
3 _ _ 7 _ _ _ _ _
_ _ 8 _ 4 _ _ _ 6
_ 2 _ _ _ 5 _ _ _
_ _ 4 _ 1 _ 6 _ _
_ _ _ 5 _ _ _ 8 _
_ 7 _ _ _ _ _ 1 _
_ 8 _ _ _ 3 9 _ _
_ _ 9 _ 8 _ _ 6 _
2 _ _ _ _ _ 4 _ 8
Proposed Solution:
3 4 1 7 6 8 2 5 9
5 9 8 2 4 1 3 7 6
6 2 7 9 3 5 8 4 1
8 5 4 3 1 7 6 9 2
1 6 2 5 9 4 7 8 3
9 7 3 8 2 6 5 1 4
4 8 6 1 5 3 9 2 7
7 3 9 4 8 2 1 6 5
2 1 5 6 7 9 4 3 8
Validity: True

```

--
### Sudoku Solver - Boilerplate
```fsharp
    // Types
    type Value = Option<int>
    type Grid = Value[,]

    let public ReadGrid filename : Grid = 
       let intToOpt x = match x with
                            | 0 -> None
                            | x -> Some x
       // Is there some better way to do this?
       let irregular = System.IO.File.ReadLines(filename) 
                           |> Seq.map (fun line -> 
                                        line.Split ' '
                                        |> Seq.map System.Int32.Parse 
                                        |> Seq.map intToOpt
                                        |> Seq.toArray)
                           |> Seq.toArray
       Array2D.init 9 9 (fun x y -> irregular.[x].[y])

    let public GridToString (grid : Grid) = 
        let intOptionToString = function
                                | None -> "_"
                                | Some x -> string x
        let printRow row = 
            Array.map intOptionToString row 
                |> String.concat " " 
            
        [for row in 0..8 do yield grid.[row, *]]
            |> List.map printRow
            |> String.concat "\n"

```
--
### Sudoku Solver - Helpers
```fsharp
    let public ValidList (list : Value list) = 
        let rec withinRange = function
            | [] -> true
            | head :: tail -> let headWithinRange = match head with
                                                    | None -> true
                                                    | Some x ->  x > 0 && x < 10
                              headWithinRange && withinRange(tail)
        let someList = list |> List.choose id
        (list.Length = 9) && (withinRange list) && (someList.Length = (List.distinct someList |> List.length))
    
    let Row row (grid : Grid) = grid.[row, *]
    let Column column  (grid : Grid) = grid.[*, column]
    let Box x y (grid : Grid) = 
        let startX = x / 3 * 3
        let startY = y /3 * 3
        grid.[startY..(startY+2), startX..(startX+2)] |> Seq.cast |> Seq.toArray

    let ValidCoordinate x y (grid : Grid) = (grid |> Row y |> Array.toList |> ValidList)
                                                && (grid |> Column x |> Array.toList |> ValidList)
                                                && (grid |> Box x y |> Array.toList |> ValidList)

    let PossibleValues x y (grid : Grid) = 
        let unusedValues list = (Set [1..9]) - (Set list)
        let unusedRow = Row y grid |> Array.choose id |> unusedValues
        let unusedColumn = Column x grid |> Array.choose id |> unusedValues
        let unusedBox = Box x y grid |> Array.choose id |> unusedValues
        unusedRow |> Set.intersect unusedColumn |> Set.intersect unusedBox    

    let VerifyGrid (grid : Grid) = 
        let valid = seq { for x in 0..8 do for y in 0..8 do yield (x, y) }
                        |> Seq.fold (fun acc (x, y) -> acc && (ValidCoordinate x y grid)) true
        let anyNone = Seq.cast<Value> grid |> Seq.exists (fun x -> match x with | None -> true | Some(_) -> false )
        valid && (not anyNone)
```
--
### Sudoku Solver Backtracing Algorithm
![Illustration](https://upload.wikimedia.org/wikipedia/commons/8/8c/Sudoku_solved_by_bactracking.gif)
```
 Initialize 2D array with 81 empty grids (nx = 9, ny = 9)
 Fill in some empty grid with the known values
 Make an original copy of the array
 Start from top left grid (nx = 0, ny = 0), check if grid is empty
 if (grid is empty) {
   assign the empty grid with values (i)
   if (no numbers exists in same rows & same columns same as (i) & 3x3 square (i) is currently in)
     fill in the number
   if (numbers exists in same rows & same columns same as (i) & 3x3 square (i) is currently in)
     discard (i) and repick other values (i++)
 }
 else {
   while (nx < 9) {
     Proceed to next row grid(nx++, ny)
     if (nx equals 9) {
       reset nx = 1
       proceed to next column grid(nx,ny++)
       if (ny equals 9) {
         print solution
       }
     }
   }
 }
```
--
### Sudoku Solver -- Solving Logic
```fsharp
    let solve (grid : Grid) =
        let solution : Grid = Array2D.copy grid
        let isValid x y = ValidCoordinate x y solution

        let rec findSolution x y = 
            match (x, y) with
                | (x, y) when x > 8 || y > 8 -> true
                | (_, _) -> let nextX, nextY = match (x, y) with
                                                | (8, y) -> 0, (y + 1)
                                                | (_, _) -> (x + 1), y
                            let currentSolution = solution.[y, x]
                            match currentSolution with
                                | Some(value) -> findSolution nextX nextY
                                | None -> let possibleValues = PossibleValues x y solution
                                          let rec explorePossibilities = function 
                                                | [] -> false
                                                | head :: tail -> solution.[y, x] <- Some head
                                                                  let valid = isValid x y
                                                                  let continuation = match valid with
                                                                                        | true -> findSolution nextX nextY
                                                                                        | false -> false
                                                                  match continuation with
                                                                        | true -> true
                                                                        | false -> solution.[y, x] <- None
                                                                                   explorePossibilities tail
                                          explorePossibilities (possibleValues |> Set.toList)                                
                            
                                      
        findSolution 0 0 |> ignore
        solution

```

--
### Next Episode
 - Function composition
 - Types
 - Sequences, lists, options, tuples
 - ["Dr Frankenfunctor and the Monadster"](https://fsharpforfunandprofit.com/posts/monadster/) -- Functors and Monads
 - Using F♯ with tests and build systems
