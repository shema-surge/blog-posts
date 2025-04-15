### How Go's error handling is better

In programming languages like java and javascript, errors are special. They are handled using fancy syntax like try and catch statements but often end up becoming messy or confusing.

Here is an example a file copying program written in javascript:

```
const fs = require("node:fs")
const { argv } = require("node:process")
try {
	const data = fs.readFileSync(argv[2], "utf-8")
    fs.writeFileSync(argv[3],data)
} catch (err) {
	console.error(err)
}
```

In the above program we try to read a text file whose name is provided as an stdin argument to the program and write its contents to a second file whose name is also provided as the second stdin argument

```
> node copy.js file1.txt file2.txt
```
The problem with this program is that if it crashed, we would not know where the error occurred from. Would it be from the readFileSync function or writeFileSync? In this scenario we are left to speculate about where error could have been thrown. 

We could try and handle errors using callbacks forexample:

```
fs.readFile(argv[2], "utf-8",(err, data)=>{
    if (err){
        console.error(err)
    }
    fs.writeFile(argv[3], data, err=>{
	if(err){
	   console.log(err)
	}
    })
})
```

but this approach quickly leads to callback hell when working with multiple risky functions.

However when it comes to Go, we are forced to think about the errors returned on each step of the way. Here is the same program now written Go.

```
package main

import (
	"log"
	"os"
)

func main() {
	data, readErr := os.ReadFile(os.Args[1])
	if readErr != nil {
		log.Fatal(readErr)
	}
	writeErr := os.WriteFile(os.Args[2], data, 0600)
	if writeErr != nil {
		log.Fatal(writeErr)
	}
}
```

In Go, we handle errors like any other value. This provides clarity into where the errors are coming from and how to handle them.
