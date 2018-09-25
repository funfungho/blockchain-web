<!-- The Go Programming Language -->
# 1. Tutorial
- > [Go Blog](https://blog.golang.org)
- > [Go Playground](https://play.golang.org)
- > [Standard library](https://golang.org/pkg)
- > [Book's source code](http://gopl.io)
- > Fetch source code 
    - `go get gopl.io/ch1/helloworld`
- Communicating Sequential Process (CSP)
    - A program is a parallel composition of processes that have no shared state; the processes communicate and synchronize using channels
- Packages
    - Go code is organized into packages 
    - A package consists of one or more `.go` source files in a single directory that define what the package does
    - Each source file begins with a `package` declaration that states which package the file belongs to, followed by a list of other packages that it imports
        - The `import` declarations must follow the `package` declaration
        - The list form of `import` declaration is conventionally used
    - Package `main` defines a standalone executable program, not a library
    - By convention, we describe each package in a comment immediately preceding its package declaration  
- Semicolons
    - Go does not require semicolons at the end of statements or declarations, except where two or more appear on the same line
    - Newlines following certain tokens are converted into semicolons
- Code formatting
    - The `gofmt` tool rewrites code into the standard format, and the `go` tool's `fmt` subcommand applies `gofmt` to all the files in the specified package, or the ones in the current directory by default
        - `gofmt` tool sorts the package names into alphabetical order
    - `goimports` manages the insertion and removal of import declarations as needed
        - > `go get golang.org/x/tools/cmd/goimports`
- Comments
    - Comments do not nest
- The value of a constant must be a number, string, or boolean
- If a variable is not explicitly initialized, it's implicitly initialized to the *zero value* of its type
- `if`
    - Go allows a simple statement such as a local variable to precede the `if` condition
        - `if err := r.ParseForm(); err != nil {...}` reduces the scope of variable `err`
- `switch`
    - Cases are evaluated from top to bottom, so the first matching one is executed
    -  The **optional** default case matches if none of the other cases does; it may be placed anywhere
    - Cases do not fall through by default (`fallthrough` statement overrides the behavior)
    - A `switch` does not need an operand; it can just list the cases, each of which is a boolean expression. This form is called a *tagless switch*; it's equivalent to `switch true`
<!-- 
    ```go
    switch coinFlip() {
        case "heads":
            heads++
        case "tails":
            tails++
        default: 
            fmt.Println("landed on edge!")
    }
    func Signum(x int) int {
        switch {
            case x > 0:
                return +1
            default: return 0
            case x < 0:
                return -1
        }
    }
    ``` 
 -->
- `for` loop
    - Any of the 3 parts can be omitted
    - The optional initialization **statement** is executed before the loop starts
<!-- 
    ```go
    for initialization; condition; post {
        // zero or more statements
    }
    // while loop
    for condition {
    }
    // infinite loop
    for {
    }
    ``` -->

- A `for`, `if` or `switch` may include an optional simple statement - a short variable declaration, an increment or assignment statement, or a function call - that can be used to set a value before it's tested
- `break`, `continue`, `goto`
    - A `break` causes control to resume at the next statement after the innermost `for`, `switch`, `select` statement
    - Statements may be labeled so that `break` and `continue` can refer to them
    - `goto` statement is intended for machine-generated code
- `map`
    - A map holds a set of **`key/value`** pairs
        - The key may be of any type whose value can be compared with `==` (string being the most common example)
        - The value may be of any type
    - A map provides constant-time operations to store, retrieve or test for an item in the set
    - The order of map iteration is **random**
- **Named types**
    
    ```go
    type Point struct {
        X, Y int
    }
    var p Point
    ```

- Function
    - Function `main` is where execution of the program begins 
    - It's a good style to write a comment before the declaration of each function to specify its behavior
- Function literal
    - An anonymous function defined at its point of use
    - `http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {...})`
- Methods
    - A method is a function associated with a named type
    - Methods may be attached to any named type
- **Interfaces**
    - Interfaces are abstract types that let us treat different concrete types in the same way based on **what methods they have**, not how they are represented or implemented 
- Pointers
    - Pointers are values that contain the address of a variable
    - `&` yields the address of a variable
    - `*` retrieves the variable that the pointer refers to
    - There's no pointer arithmetic
- ***goroutine***
    - A goroutine a concurrent function execution
    - The function `main` runs in a goroutine and  the `go` statement creates additional goroutines
    - When one goroutine attempts a send or receive on a channel, it blocks util another goroutine attempts the corresponding receive or send operation, at which point the value is transferred and both goroutines proceed
        - [ ] channel blocks, right?
- *channel*
    - A communication mechanism that allows one goroutine to pass values of a **specified type** to another goroutine
- `fmt.printf`
    - > p.10
    - `%v`: any value in natural format
    - By convention, formatting functions whose names end in `f` use the formatting rules of `fmt.Printf`, whereas those whose names ends in `ln` follow `Println`, formatting their arguments as if by `%v`, followed by a newline
- ***blank identifier***
    - `_`
    - It may be used whenever syntax requires a variable name but program logic does not
- Documentation
    - `godoc -http=:8080`
    - `go doc strconv.Atoi`
- Command-Line Arguments
    - Command-line arguments are available to a program in a variable named `Args` that is part of the `os` package
    - The first element of `os.Args`, `os.Args[0]` is the name of the command itself
        - `os.Args[1:len(os.Args)]`, `os.Args[1:]`
        - > `s[m:n]` yields a slice that refers to elements `m` through `n-1`
            - If `m` or `n` is omitted, it defaults to 0 or `len(s)` respectively
## Demos
- echo
    - `-n` causes `echo` to omit the trailing newline that would normally be printed; `-s sep` causes it to separate the output arguments by the contents of the string `sep` instead of the default single space
<!-- 
    ```go
    // echo1
    func main() {
        var s, sep string
        for i := 1; i < len(os.Args); i++ {
            s += sep + os.Args[i]
            sep = " "
        }
        fmt.Println(s)
    }
    // echo2
    func main() {
        s, sep := "", ""
        // it's an initialization
        for _, arg := range os.Args[1:] {   // range produces a pair of value in each iteration of the loop
            s += sep + arg
            sep = " "
        }
        fmt.Println(s)
    }
    // echo3
    func main() {
        fmt.Println(strings.Join(os.Arg[1:], " "))
    }
    // echo4
    /*  `flag.Bool` creates a new flag variable of type bool. It takes 3 arguments: the name of the flag `"n"`, the variable's default value (`false`), a message that will be printed if the user provides an invalid argument, an invalid flag, or `-h` 
    ** or `-help`
    ** `flag.String` takes name, a default value, and a message, and creates a string variable
    ** the variable `sep` and `n` are pointers to the flag variable
    ** When the program is run, it must call `flag.Parse` before the flags are used, to update the flag varialbes from their default
    ** values. - [ ] The non-flag arguments are available from `flags.Args()` as a slice of strings
    ** If `flag.Parse()` encounters an error, it prints a usage message and calls `os.Exit(2)` to terminate the program
    */
    var n = flag.Bool("n", false, "omit trailing newline")
    var sep = flag.String("s", " ", "separator")
    func main() {
        flag.Parse()
        fmt.Print(strings.Join(flag.Args(), *sep))
        if !*n {
            fmt.Println()
        }
    }
    ```
 -->
- dup
    - `Sacnner` reads input and breaks it into lines or words
    - When the end of the input is reached, `Close` closes the file and releases any resources
    - When a map is passed into a function, the function receives **a copy of the reference**, so any change the called function makes to the underlying data structure will be visible through the caller's map reference too
<!-- 
    ```go
    // dup1
    func main() {
        counts := make(map[string]int) // key string, value int
        input := bufio.NewScanner(os.Stdin)
        // each call to `input.Scan()` reads the next line and removes the newline character from the end
        // return true if there's a line and false when there's no more input
        // the result can be retrieved by calling `input.Text()`
        for input.Scan() {
            // the first time a new line is seen, counts[line] evaluates to the zero value for its type
            counts[input.Text()]++
            /* line := input.Text()
            counts[line] += 1 */
        }
        for line, n := range counts {
            if n > 1 {
                fmt.Printf("%d\t%s\n", n, line)
            }
        }
    }

    // dup2
    func main() {
        counts := make(map[string]int)
        files := os.Args[1:]
        if len(files) == 0 {
            countLines(os.Stdin, counts)
        } else {
            for _, arg := range files {
                f, err := os.Open(arg)
                if err != nil {
                    fmt.Fprintf(os.Stderr, "dup2: %v\n", err)
                    continue
                }
                countLines(f, counts)
                f.Close()
            }
        }
        for line, n := range counts {
            if n > 1 {
                fmt.Printf("%d\t%s\n", n, line)
            }
        }
    }
    func countLines(f *os.File, counts map[string]int) {
        input := bufio.NewScanner(f)
        for input.Scan() {
            counts[input.Text()]++
        }
    }

    // dup3
    func main() {
	counts := make(map[string]int)
	for _, filename := range os.Args[1:] {
		data, err := ioutil.ReadFile(filename)
		if err != nil {
			fmt.Fprintf(os.Stderr, "dup3: %v\n", err)
			continue
        }
        // `ReadFile` returns a byte slice that must be converted into a string so it can be split by `strings.split`
		for _, line := range strings.Split(string(data), "\n") {
			counts[line]++
		}
        }
        for line, n := range counts {
            if n > 1 {
                fmt.Printf("%d\t%s\n", n, line)
            }
        }
    }
    ``` 
-->
- [ ] lissajous    
    - `[]color.Color{}` (slice) and `gif.GIF{...}` (struct) are **composite literals**
<!-- 
    ```go
    var palette = []color.Color{color.White, color.Black}

    const (
        whiteIndex = 0 // first color in palette
        blackIndex = 1 // next color in palette
    )
    func main() {
        lissajous(os.Stdout)
    }
    func lissajous(out io.Writer) {
        const (
            cycles  = 5
            res     = 0.001
            size    = 100
            nframes = 64
            delay   = 8
        )
        // generates a random number between 0 and 3
        freq := rand.Float64() * 3.0
        anim := gif.GIF{LoopCount: nframes}
        phase := 0.0
        // each outer loop producing a single frame of the animation
        for i := 0; i < nframes; i++ {
            rect := image.Rect(0, 0, 2*size+1, 2*size+1)
            // all pixels are initially set to the palette's zero value (the zeroth color in the palette), which is white - [ ] later
            img := image.NewPaletted(rect, palette)
            for t := 0.0; t < cycles*2*math.Pi; t += res {
                x := math.Sin(t)
                y := math.Sin(t*freq + phase)
                img.SetColorIndex(size+int(x*size+0.5), size+int(y*size+0.5), blackIndex)
            }
            phase += 0.1
            anim.Delay = append(anim.Delay, delay)
            anim.Image = append(anim.Image, img)
        }
        gif.EncodeAll(out, &anim)
    }
    ``` 
-->
- concurrent fetch
    - Having one `main` do all the printing ensures that output from each goroutine is processed as a unit (blocked), with no danger of interleaving if two goroutine finishes at the same time
<!--     
    ```go
    func main() {
	    start := time.Now()
        ch := make(chan string)
        for _, url := range os.Args[1:] {
            go fetch(url, ch) // start a goroutine
        }
        for range os.Args[1:] {
            fmt.Println(<-ch) // receive from channel ch
        }
        fmt.Printf("%.2fs elapsed\n", time.Since(start).Seconds())
    }
    func fetch(url string, ch chan<- string) {
        start := time.Now()
        resp, err := http.Get(url)
        if err != nil {
            ch <- fmt.Sprint(err)
            return
        }
        // io.Copy function reads the body of the response and discards it by writing to ioutil.Discard output stream
        nbytes, err := io.Copy(ioutil.Discard, resp.Body)
        resp.Body.Close()
        if err != nil {
            ch <- fmt.Sprintf("while reading %s: %v", url, err)
            return
        }
        secs := time.Since(start).Seconds()
        ch <- fmt.Sprintf("%.2fs  %7d  %s", secs, nbytes, url)
    }
    ``` 
-->
- server
    - Behind the scenes, server2 runs the handler for each incoming request in a separate goroutine so it can serve multiple requests simultaneously
        - If 2 concurrent request try to update `count` at the same time, it might not be incremented consistently (race condition)
        - Must ensure at most one goroutine accesses the variable at a time
<!-- 
    ```go
    // server 1
    func main() {
        http.HandleFunc("/", handler)
        log.Fatal(http.ListenAndServe("localhost:8000", nil))
    }
    // a request is represented as a struct of type `http.Request`
    func handler(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, " URL.Path = %q\n", r.URL.Path)
    }

    // server 2
    func main() {
        // a request for /count invokes counter and all others invoke handler
        http.HandleFunc("/", handler)
        http.HandleFunc("/count", counter) // excluding /count requests themselves
        log.Fatal(http.ListenAndServe("localhost:8000", nil))
    }
    func handler(w http.ResponseWriter, r *http.Request) {
        mu.Lock()
        count++
        mu.Unlock()
        fmt.Fprintf(w, "URL.Path = %q\n", r.URL.Path)
    }
    func counter(w http.ResponseWriter, r *http.Request) {
        mu.Lock()
        fmt.Fprintf(w, "Count %d\n", count)
        mu.Unlock()
    }
    ``` 
-->
# Program structure
- Variables store value. Simple expressions are combined into larger ones with operation like addition and subtraction. Basic types are collected into aggregates like arrays and structs. Expressions are used in statements whose execution order is determined by control-flow statements like `if` and `for`. Statements are grouped into functions for isolation and reuse. Functions are gathered into source files and packages
## Names
- The name of Go functions, variables, constants, types, statement labels, and packages follow a simple rule: a name begin with a letter (that is, anything that Unicode deems a letter) or an underscore and may have any number of additional letters, digits, and underscores
- Go has 2 keywords. They cannot be used as names
    - `break`, `case`, `chan`, `const`, `continue`, `default`, `defer`, `else`, `fallthrough`, `for`, `func`, `go`, `goto`, `if`, `import`, `interface`, `map`, `package`, `range`, `return`, `select`, `struct`, `switch`, `type`, `var`
- Go has about a dozen ***predeclared names*** like `int` and `true` for built-in constants, types, and functions
    - These names are not reserved, there're a handful of places where redeclaring one of them makes sense
- Scope
    - An entity declared within a function is local to that function. An entity declared outside a function is visible in **all files of the package** to which it belongs
- Visibility
    - The case of the first letter of a name determines its visibility ***across package boundaries***
    - A name beginning with an upper-case letter is exported. That means it's visible and accessible outside of its own package and may be referred to by other parts of the program
- Package names are always in lower case
- There' no limit on name length
    - Go programs lean toward short names, especially for local variables with small scopes
    - Generally, the larger the scope of a name, the longer and more meaningful it should be
- Use "camel case"
- The letters of acronyms and initialisms are always rendered **in the same case**
    - `htmlEacape`, `HTMLEscape`
## Declarations
- A declaration names a program entity and specifies some or all of its property
- 4 major kinds of declarations: `var`, `const`, `type`, `func`
-- Functions and other package-level entities may be declared **in any order**
- (`const`, `var`) Declarations may appear at package level(so the names are visible throughout the package) or within a function(so the names are visible only within that function)
## Variables
- A variable is a piece of storage containing a value
- A var declaration creates a variable of a particular type, attaches a name to it, and sets its initial value
    - `var name type = expresssion`
    - Either the `type` or the `= expression` can be omitted, but not both
        1. If the expression is omitted, the initial value is the *zero value* for its type
- Initializers may be literal values or arbitrary expressions
    - Package-level variables are initialized **before `main` begins**
    - Local variables are initialized as their declarations are encountered during function execution
- It's possible to declare and optionally initialize a set of variables in a single declaration
- A set of variables can also be initialized by calling a function that returns multiple values

    ```go
    var i, j, k int // int int int
    var b, f, s = true, 2.3, "four" // bool, float64, string
    var f, err = os.Open(name)  // returns a file and an error
    ```

- A `var` declaration tends to be reserved for local variables that need an explicit type that differs from that of the initializer expression - [ ], or for when the variable will be assigned a value later and its initial value is unimportant
- ***zero vale***
    - numeric type: 0
    - string: `""`
    - boolean: `false`
    - interfaces, reference types(slice, pointer, map, channel, function): `nil`
    - aggregate type (array, struct): zero value of all of its elements or fields
### Short Variable Declarations
- `name := expression`
    - Used within a function only, not for package-level variables
    - The type of `name` is determined by the type of `expression`
    - Used to declare and initialize the majority of local variables
- **`:=` is a declaration, `=` is an assignment**
- `i, j := 0, 1`
    - Declarations with multiple initializer expressions should be used only when they help readability (such as for short and natural groupings like initialization part of a `for` loop)
    - > tuple assignment: `i, j = j, i` swaps values of `i` and `j`
- `f, err := os.Open(name)`
- A short variable declaration **must declare at least one new variable**
    - If some of variables in a short variable declaration were already declared **in the same lexical block**, then,  for those variables, the short variable declaration acts like an **assignment**
    - A short variable declaration acts like an assignment only to variables that were already in the same lexical scope; declarations in an outer block are ignored
- Declare a string variable
    1. `s := ""`
        - can be used only within a function, not for package-level variables
    2. `var s string`
        - relies on default initialization to the zero value
    3. `var s = ""`
        - rarely used except when declaring multiple variables
    4. `var s string = ""`
        - explicit about the variable's type
        - redundant when variable type is the same as that of the initial value
        - necessary when they are not of the same type
### Pointers
- Variables created by declarations are identified by a name, such as `x`, but many variables are identified only by expressions like `x[i]` or `x.f`
- A pointer value is the address of a variable. A pointer is thus the **location** at which a value is stored
    - Not every value has an address, but every variable does
    - With a pointer, we can read or update the value of a variable indirectly, without using or even knowing the name of the variable, if indeed it has a name
- If a variable is declared `var x int`, the expression `&x` ("address of x") yields a value to an integer variable, that is, a value of type `*int`, which is pronounced "pointer to int"
    - If this value is called `p`, we say "`p` points to `x`", or equivalently "`p` contains the address of `x`"
    - The variable to which `p` points is written `*p`. The expression `*p` yields the value of that variable, an `int`
    - Since `*p` denotes a variable, it may also appear on the left-hand side of an assignment, in which case the assignment updates the variable
- Each component of a variable of aggregate type - a field of a struct or an element of an array - is also a variable and thus also has an address too
- The zero type of a pointer of any type is `nil`
    - `var p *int`
- Pointers are **comparable**
    - Two pointers are equal if and only if they point to the same variable or both are nil
    - `p != nil` is `true` if `p` points to a variable
- It's safe for a function to return the address of a local variable
    - The local variable will remain in existence even after the call has returned if there's a pointer still refers to it
    - Each call to `f()` returns a distinct value
<!-- 
    ```go
    func main() {
        fmt.Println(f() == f())
    }
    func f() *int {
        v := 1
        return &v
    }
    ```
 -->
 - Each time we take the address of a variable or copy a pointer, we create new **aliases** or ways to identify the same variable
    - To find all the statements that access a variable, we have to know all its alias
    - It's not just pointers that create aliases; aliasing also occurs when we copy values of other reference types like slices, maps, and channels, and even structs, arrays, and interfaces that contains these types
### the `new` function
- Another to create a variable is to use the built-in function `new`
- `new(T)` creates an **unnamed** variable of type `T`, initializes it to the zero value of `T`, and returns its **address**, which is a value of type `*T`
    - A variable created with `new` is no different from an ordinary local variable whose address is taken, except that there's no need to invent (and declare) a dummy name
    - Thus the `new` is only a syntactic convenience
- Each call to `new` returns a distinct variable with a unique address
    - One exception: two variables whose type carries no information and is therefore of size zero, such as `struct{}` or `[0]int`, may, depending on the implementation, have the same address
### Lifetime of variables
- The lifetime of a package-level variable is the entire execution of the program
- Local variables have dynamic lifetimes: a new instance is created each time the declaration statement is executed, and the variable lives on until it becomes unreachable, at which point its storage may be recycled
    - Function parameters and results are local variables too; they are created each time their enclosing function is called
- Garbage collector
    - Every package-level variable, and every local variable of each currently active function, can potentially be the start or root of a path to the variable in question, following pointers and other kinds of references that ultimately lead to the variable
    - If no such path exists, the variable has become unreachable, so it can no longer affect the rest of the computation
- A complier may choose to allocate local variables on the heap or the stack
    - `x` must be heap-allocated because it's still reachable from the variable `global` after `f` has returned; we say `x` escapes from `f`
    - It's safe for the complier to allocate `*y` on the stack, even though it was allocated with `new`
    - Keeping unnecessary pointers to short-lived objects within long-lived objects, especially global variables, will prevent the GC from reclaiming the short-lived objects

    ```go
    var global *int
    func f() {
        var x int
        x = 1
        global = &x
    }
    func go() {
        y := new(int)
        *y = 1
    }
    ```

## Assignments
- The value held by a variable is updated by an assignment statement, which in its simplest form has a variable on the left of the sign `=` sign and an expression on the right
- `++`, `--`
    - postfix only
    - `i++` is a statement, not an expression
        - Thus `j = i++` is illegal
- Each of the arithmetic and binary operators has a corresponding assignment operator allowing (`*=`)
### Tuple assignments
- All of the right-hand expression are evaluated before any of the variables are updated, making this form most useful when some of the variables appear on both sides of the assignment
<!-- 
    ```go
    // GCD (greatest common divisor)
    func gcd(x, y int) int {
        for y != 0 {
            x, y = y, x%y
        }
        return x
    }
    // n-th Fibonacci number
    func fib(n int) int {
        x, y := 0, 1
        for i := 0; i < n; i++ {
            x, y = y, x+y
        }
        return x
    }
    ``` 
-->
- Certain expressions, such as a call to a function with multiple results,produce several values
    - When such a call is used in an assignment statement, the left-hand side must have as many variables as the function has results
    - Often, functions use these additional results to indicate some kind of error, either by returning an `error`, or a `bool`, usually called `ok`
- There're 3 operators that sometimes behave this way too
    - If a map lookup, type assertion, or channel receive appears in an assignment in which two results are **expected**, each produces an additional boolean result
<!-- 
    ```go
    v, ok = m[key]
    v, ok = x.(T)
    v, ok = <-ch
    ```
 -->
- As with variable declarations, we can assign unwanted values to the blank identifier
### Assignability
- Assignment statements are an explicit form of assignment
- Places where assignment occurs implicitly
    - A function call implicitly assigns the values to the corresponding parameter variables
    - A `return` statement implicitly assigns the `return` operands to the corresponding result variables
    - A literal expression for a composite type 
        - `medals := []string{"gold", "silver", "bronze"}`
        - The elements of maps and channels, though not ordinary variables, are also subject or implicit assignments
- An assignments is always legal if the left-hand side (the variable) and the right-hand side (the value) have the same type. More generally, the assignment is legal only if the value is *assignable* to the type of the variable
- The rule for assignability
    - For the types we've discussed so far, the types must exactly match
    - `nil` may be assigned to any variable of interface or reference type
    - Constants have more flexible rules for assignability that avoid the need for most explicit conversions
## Type Declarations
- A `type` declaration defines a new named type that has the same underlying types as an existing type
    - `type name underlying-type`
- The named types provides a way to separate different and perhaps incompatible uses of the underlying types so that they can't be mixed unintentionally
    - Even though `Celsius` and `Fahrenheit` have the same underlying type, they are not the same type, so they cannot be be compared or combined in arithmetic expressions
    - Distinguishing the types makes it possible to avoid errors like inadvertently combining temperatures in the 2 different scales
    - An explicit type conversion like `Celsius(t)` or `Fahrenheit(t)` is required to convert from a `float64`. 
    - `Celsius(t)` and  `Fahrenheit(t)` are conversions, not function calls. They don't change the value or representation in any way, but they make the change of meaning explicit
<!-- 
    ```go
    type Celsius float64
    type Fahrenheit float64
    const (
        AbsoluteZeroC Celsius = -273.15
        FreezingC     Celsius = 0
        BoilingC      Celsius = 100
    )
    func CToF(c Celsius) Fahrenheit { return Fahrenheit(c*9/5 + 32) }
    func FToC(f Fahrenheit) Celsius { return Celsius((f - 32) * 5 / 9) }
    ```
 -->
 - For every type `T`, there's a corresponding conversion operation `T(x)` that converts the value `x` to type `T`
 - A conversion from one type to another type is allowed if **both have the same underlying type**, or if both are **unnamed pointer** types that point to variables of the same underlying type
    - These conversions change the type but not the representation of the value
    - If `x` is assignable to `T`, a conversion is permitted but is usually redundant
- Conversions are allowed between numeric types, and between string and some slice types
    - These conversions may change the representation of the value
        - Converting a floating-point number to an integer discards any fractional part
        - Converting a string to a `[]byte` slice allocates a copy of the string data
- The underlying type of a named type determines its structure and representation, and also the set of intrinsic operations it supports, which are the same as if the underlying type had been used directly
- Comparison operators like `==` and `<` can also be used to compare a value of a named type to another of the same type, or to a value of of an unnamed type with the same underlying type
- Named types also make it possible to define new behaviors for values of the type. These behaviors are expressed as a set of functions associated with the type, called the type's methods
    - `func (c Celsius) String() string { return fmt.Sprintf("%g°C", c) }`
    - The `Celsius` parameter `c` appears before the function name, associate with the `Celsius` type a method named `String` that returns `c`'s numeric value followed by °C
## Packages and Files