[![PkgGoDev](https://pkg.go.dev/badge/hashcache)](https://pkg.go.dev/hashcache)

# hashcache

hashcache is an experiment in creating a hash table / KV store from first principles.

It uses [SipHash](https://131002.net/siphash/) for hashing (which may be a terrible choice!)

To install:

```console

$ go get github.com/Intermernet/hashcache
```

To use:

```
package main

import (
	"fmt"
	"log"

	"github.com/Intermernet/hashcache"
)

type kv struct {
	key   []byte
	value []byte
}

var kvs = []kv{
	{[]byte("data1"), []byte("some large data")},
	{[]byte("data2"), []byte("some more large data")},
	{[]byte("data3"), []byte("even more large data")},
}

func main() {

	c := hashcache.NewCache("Some unique 128 bit key")
	// Set the TTL
	if err := c.SetTTL(5000); err != nil {
		log.Fatalf("%v\n", err)
	}
	// Set the scavenge time
	if err := c.SetScavengeTime(1000); err != nil {
		log.Fatalf("%v\n", err)
	}
	// Add some data
	for _, kv := range kvs {
		c.Write(kv.key, kv.value)
	}
	// Read some data
	for _, kv := range kvs {
		value, ok := c.Read(kv.key)
		switch {
		case ok:
			fmt.Printf("\"%s\" found in cache: \"%s\"\n", kv.key, value)
		default:
			fmt.Printf("\"%s\" not found in cache\n", kv.key)
		}
	}
	// Delete some data
	for _, kv := range kvs {
		ok := c.Delete(kv.key)
		fmt.Printf("%s was deleted: %t\n", kv.key, ok)
	}

}
```

# Todo:

- Tests!
- Benchmarks
- A whole lot more