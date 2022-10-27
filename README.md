# go-goroutine-pool
An implementation of a lightweight grouting thread built using a lock-free stack.

by limiting concurrency with a fixed pool size and recycling goroutines using a stack, go-goroutine-pool saves on memory as compared to a an more traditional goroutine, while preserving a similar level of speed.

This library has not yet been benchmarked and should be soon.

## Installation

You need Golang [1.19.x](https://go.dev/dl/) or above

```bash
$ go get github.com/micihs/go-goroutine-pool
```


## Usage

This code can be found in the examples directory of this repository.

```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
	"time"

	"github.com/alphadose/itogami"
)

const runTimes uint32 = 1000

var sum uint32

func myFunc(i uint32) {
	atomic.AddUint32(&sum, i)
	fmt.Printf("run with %d\n", i)
}

func demoFunc() {
	time.Sleep(10 * time.Millisecond)
	println("Hello World")
}

func examplePool() {
	var wg sync.WaitGroup
	// Use the common pool
	pool := goroutine.NewPool(10)

	syncCalculateSum := func() {
		demoFunc()
		wg.Done()
	}
	for i := uint32(0); i < runTimes; i++ {
		wg.Add(1)
		// Submit task to the pool
		pool.Submit(syncCalculateSum)
	}
	wg.Wait()
	println("finished all tasks")
}

func examplePoolWithFunc() {
	var wg sync.WaitGroup
	// Use the pool with a pre-defined function
	pool := goroutine.NewPoolWithFunc(10, func(i uint32) {
		myFunc(i)
		wg.Done()
	})
	for i := uint32(0); i < runTimes; i++ {
		wg.Add(1)
		// Invoke the function with a value
		pool.Invoke(i)
	}
	wg.Wait()
	fmt.Printf("finish all tasks, result is %d\n", sum)
}

func main() {
	examplePool()
	examplePoolWithFunc()
}
```
