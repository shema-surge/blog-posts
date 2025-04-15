### How Go's error handling is better

In many programming languages like java and javascript, errors are special.They are handled using fancy syntax like try and catch statements but often end up becoming messy or confusing.

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

In the above program we try to read a text file whose name is provided as an stdin argument to the program and write its contents to a second file whose name is also provided as the second argument

The problem in this program is that we do not know where the error occured, was it the readFileSync function that returned an error? or was it writeFileSync? The error could be anything. 

We could try and handle errors using callbacks forexample:

```
fs.readFile(argv[2], "utf-8",(err, data)=>{
    if (err){
        console.error(err)
    }
    fs.writeFile(argv[3], data, err=>{
        console.log(err)
    })
})
```

but this approach quicky leads to callback hell when working with multiple risky functions.

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
		//do something with the read error
		log.Fatal(readErr)
	}
	writeErr := os.WriteFile(os.Args[2], data, 0600)
	if writeErr != nil {
		//do something with the write error
		log.Fatal(writeErr)
	}
}
```

We handle errors as any other value by checking if they are equivalent to null or nil in Go's case. This provides clarity as to where the error is coming from and also allows more flexibility in handling the error.
