---
layout: post
title: "[Golang] Coroutine"
description: "new jounery to go"
comments: true
keywords: "go, programming language"
---

reference: https://golangbot.com/interfaces-part-2/


### Adv of goroutine over thread?
- cheap. a few kb in stack size; stack can grow and shrink according to the needs of the application; stack size of thread is fixed.
- abstract
- channels, shared memory 



return immediately, any return values from the Goroutine are ignored.

main terminates, all terminates. 


// channels: pipes where goroutines communicate

```go
data := <- a // read from channel a  
a <- data // write to channel a  
```
##### Sends and receives are blocking by default
Sends and receives to a channel are blocking by default. What does this mean? When data is sent to a channel, the control is blocked in the send statement until some other Goroutine reads from that channel. Similarly, when data is read from a channel, the read is blocked until some Goroutine writes data to that channel.



##### send only receive only
This is where channel conversion comes into use. It is possible to convert a bidirectional channel to a send only or receive only channel but not the vice versa.



Closing channels and for range loops on channels 
to break out of the range loops. 

```go
package main

import (  
    "fmt"
)

func producer(chnl chan int) {  
    for i := 0; i < 10; i++ {
        chnl <- i
    }
    close(chnl)
}
func main() {  
    ch := make(chan int)
    go producer(ch)

    ch := make(chan int)
    go producer(ch)
    for v := range ch {
        fmt.Println("Received ",v)
    }


    //for {
    //    v, ok := <-ch
    //    if ok == false {
    //     break
   //     }
   //   fmt.Println("Received ", v, ok)
 //   }
}

```



a great example about go routine and channel, producer and consumer model
```go
package main

import (
	"fmt"
	"math/rand"
	"sync"
	"time"
)

type Job struct {
	id       int
	randomno int
}
type Result struct {
	job         Job
	sumofdigits int
}

var jobs = make(chan Job, 10)
var results = make(chan Result, 10)

func digits(number int) int {
	sum := 0
	no := number
	for no != 0 {
		digit := no % 10
		sum += digit
		no /= 10
	}
	time.Sleep(2 * time.Second)
	return sum
}
func worker(wg *sync.WaitGroup) {
	for job := range jobs {
		output := Result{job, digits(job.randomno)}
		results <- output
	}
	wg.Done()
}
func createWorkerPool(noOfWorkers int) {
	var wg sync.WaitGroup
	for i := 0; i < noOfWorkers; i++ {
		wg.Add(1)
		go worker(&wg)
	}
	wg.Wait()
	close(results)
}
func allocate(noOfJobs int) {
	for i := 0; i < noOfJobs; i++ {
		randomno := rand.Intn(999)
		job := Job{i, randomno}
		jobs <- job
	}
	close(jobs)
}
func result(done chan bool) {
	for result := range results {
		fmt.Printf("Job id %d, input random no %d , sum of digits %d\n", result.job.id, result.job.randomno, result.sumofdigits)
	}
	done <- true
}
func main() {
	startTime := time.Now()
	noOfJobs := 100
	go allocate(noOfJobs)
	done := make(chan bool)
	go result(done)
	noOfWorkers := 10
	createWorkerPool(noOfWorkers)
	<-done
	endTime := time.Now()
	diff := endTime.Sub(startTime)
	fmt.Println("total time taken ", diff.Seconds(), "seconds")
}

```
