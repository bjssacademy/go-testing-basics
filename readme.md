# Testing with Go

In Go, tests are written in a separate file ending with `_test.go`. The standard Go testing package (`testing`) provides support for writing unit tests. 

1. Test Functions
In Go, test functions are regular functions that start with the word `Test`, followed by a descriptive name and a parameter of type `*testing.T`. For example:

```go
func TestMyFunction(t *testing.T) {
    // Test code goes here
}
```

2. Test File Structure
Test files typically mirror the package structure of the code being tested. For example, if your code is in a package named `mypackage`, your test file would be named `mypackage_test.go`.

3. Test Assertions
Inside test functions, you use the `*testing.T` parameter to report whether a test has passed or failed using assertion functions like `t.Errorf()`, `t.Fail()`, `t.FailNow()`, `t.Log()`, etc.

4. Running Tests
You can run tests using the go test command followed by the package or directory containing your tests. For example:

```bash
go test ./...
```

This command will recursively run all tests in the current directory and its subdirectories.

## Example of Testing Using Go

```go
package mypackage

import (
    "testing"
)

func Add(a, b int) int {
    return a + b
}

func TestAdd(t *testing.T) {
    a := 1
    b := 2
    want := 3

    got := Add(a, b)
    if got != want {
        t.Errorf("Add(%d, %d) = %d; want %d", a, b, got, want)
    }
}
```

5. Table-Driven Tests
Go encourages table-driven tests, where you provide input-output pairs as test cases in a table format. This makes it easy to add, modify, and understand test cases.

## Example of Table-Driven test in Go

```go
package mypackage

import (
    "testing"
)

func Add(a, b int) int {
    return a + b
}

func TestAdd(t *testing.T) {
    // Test cases
    tests := []struct {
        a, b, want int
    }{
        {1, 2, 3},
        {0, 0, 0},
        {-1, 1, 0},
    }

    for _, tc := range tests {
        got := Add(tc.a, tc.b)
        if got != tc.want {
            t.Errorf("Add(%d, %d) = %d; want %d", tc.a, tc.b, got, tc.want)
        }
    }
}

```
---

# Test Driven Development

Test-Driven Development (TDD) is a software development approach where tests are written before the actual implementation code. The TDD cycle typically follows three steps.

1. Write a Failing Test

First, you write a test case that describes the behaviour you want to implement. This test should fail initially because the corresponding functionality hasn't been implemented yet.

2. Write the Code

Next, you write the minimum amount of code required to make the failing test pass. The goal is to write code that fulfills the requirements of the test case and nothing more.

3. Refactor

Once the test passes, you can refactor your code to improve its design, readability, or performance. During refactoring, you ensure that the existing tests continue to pass.

> This is known as the Red-Green-Refactor loop.

## Worked Example

### Problem Statement
Let's say we want to implement a function that checks whether a given number is prime or not.

### Step 1 - Write a Failing Test
First, we'll write a failing test to describe the *behaviour* we want from our prime number checker function.

```go
package prime

import "testing"

func TestIsPrime(t *testing.T) {
    if !IsPrime(2) {
        t.Error("Expected 2 to be prime")
    }
    if IsPrime(4) {
        t.Error("Expected 4 not to be prime")
    }
}
```

### Step 2 - Write the Code
Now, let's implement the `IsPrime()` function to make the test pass.

```go
package prime

func IsPrime(n int) bool {
    if n <= 1 {
        return false
    }
    for i := 2; i*i <= n; i++ {
        if n%i == 0 {
            return false
        }
    }
    return true
}
```

### Step 3 - Refactor (if needed)
Since our implementation is simple, there's no need for refactoring in this case. However, if our function were more complex or if we wanted to improve its efficiency, we could refactor it while ensuring that the test *continues to pass*.

### Loop Round - Add More Tests
We can enhance our test suite by adding more test cases to cover different scenarios, such as negative numbers, large prime numbers, or composite numbers.

```go
func TestIsPrime(t *testing.T) {
    testCases := []struct {
        input  int
        output bool
    }{
        {2, true},
        {4, false},
        {-1, false},
        {13, true},
        {15, false},
        {999983, true}, // Large prime
    }

    for _, tc := range testCases {
        if IsPrime(tc.input) != tc.output {
            t.Errorf("IsPrime(%d) returned incorrect result", tc.input)
        }
    }
}
```

---

# Task

We are going to create a *package* in Go to wrap the I/O functions for dealing with files (created, delete, etc).

You *must* use a TDD approach to this problem.

## Specification

### Purpose
The purpose of the File Management Tool is to provide users with a simple interface to manage files within a specified directory. It allows users to create, read, update, and delete files.

### Features
1. Users can create a new file within the specified directory. They provide the filename and the content of the file.

2. Users can read the content of an existing file within the specified directory. They provide the filename and receive the content of the file.

3. Users can update the content of an existing file within the specified directory. They provide the filename and the new content of the file.

4. Users can delete an existing file within the specified directory. They provide the filename to delete.

### Constraints
The File Management Tool operates within the specified directory only. Users cannot access files outside this directory.

The tool does not support directories within directories. It only works with files directly within the specified directory.

File names must be unique within the specified directory. Attempting to create a file with an existing filename will result in an error.

### User Interface
The File Management Tool does not have a graphical user interface. Users interact with it through function calls in their Go programs.

### Error Handling
The tool provides appropriate error messages for common error scenarios, such as file not found, permission denied, etc. Users are expected to handle these errors in their programs.

### Security
The File Management Tool does not perform any security checks. Users are responsible for ensuring that they have appropriate permissions to perform file operations within the specified directory.

### Dependencies
The File Management Tool depends on the standard Go packages `os` and `io/ioutil` for file operations.

### Testing
The File Management Tool is thoroughly tested using unit tests to ensure that each function operates correctly under various scenarios, including file creation, reading, updating, and deletion.

### Usage
Users can import the filemanager package into their Go programs and create an instance of the FileManager struct, passing the root directory as an argument to the constructor. They can then use the provided methods to perform file operations within that directory.

#### Example Usage
```go
package main

import (
    "fmt"
    "github.com/yourusername/filemanager"
)

func main() {
    // Create a FileManager instance with the root directory "/tmp"
    fm := filemanager.NewFileManager("/tmp")

    // Create a new file
    err := fm.CreateFile("test.txt", []byte("Hello, world!"))
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Println("File created successfully")

    // Read the content of the file
    content, err := fm.ReadFile("test.txt")
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Println("File content:", string(content))

    // Update the content of the file
    err = fm.UpdateFile("test.txt", []byte("Hello, Gopher!"))
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Println("File updated successfully")

    // Delete the file
    err = fm.DeleteFile("test.txt")
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Println("File deleted successfully")
}
```

---

# Integration Testing In Go

Whilst our unit tests don't need the code to be running, integration tests are the next step out from our code with lots of parts being *integrated* together.

One example is testing an API.

Create a new Go folder and put the following in your `main.go` file:

```go
package main

import (
    "fmt"
    "log"
    "net/http"
)

func main() {
    http.HandleFunc("/hello", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello, World!")
    })

    log.Fatal(http.ListenAndServe(":8080", nil))
}
```

This will start a webserver when you run it on localhost with port 8080. If you open up "http://localhost:8080" in a browser, you will see the text "Hello, World!" displayed.

Because this code needs to be running to check that it works, we'll need to do something different in our tests.

## An Integration Test

We need to make HTTP requests to our test server and verify the response.

```go
package main_test

import (
    "net/http"
    "testing"
)

func TestHelloHandler(t *testing.T) {
    // Start a test server
    go main()

    // Make a GET request to the /hello endpoint
    resp, err := http.Get("http://localhost:8080/hello")
    if err != nil {
        t.Fatalf("Failed to make GET request: %v", err)
    }
    defer resp.Body.Close()

    // Check if the status code is 200 OK
    if resp.StatusCode != http.StatusOK {
        t.Errorf("Expected status code 200, got %d", resp.StatusCode)
    }

    // Read the response body
    body, err := io.ReadAll(resp.Body)
    if err != nil {
        t.Fatalf("Failed to read response body: %v", err)
    }

    // Check if the response body matches the expected value
    expected := "Hello, World!"
    if string(body) != expected {
        t.Errorf("Expected response body %q, got %q", expected, string(body))
    }
}

```

And we execute this test with `go test` on the command line in the root of our app.

## Advanced

Clearly these test aren't great. Our API is likely to be sending back complex JSON which we will want to check or manipulate in some way.


For that, we'll need to [marshal and unmarshal data](https://betterstack.com/community/guides/scaling-go/json-in-go/), which is covered in the link.

