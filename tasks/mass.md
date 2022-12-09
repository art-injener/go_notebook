### Итерация по массиву

Итерация с копированием массива

```go
type myStruct struct {
    h     uint64
    cache [64]byte
    raw   []byte
}

func main() {
    sl := [5]myStruct{}

    for i := 0; i < 5; i++ {
        sl[i].h = uint64(i)
        sl[i].raw = make([]byte, 1024*1024)
    }
	
    var sum uint64 = 0
    for _, v := range sl {
        sum += v.h
   }
}
```

Итерация без копирования

1. Вариант

```go
type myStruct struct {
    h     uint64
    cache [64]byte
    raw   []byte
}

func main() {
    sl := [5]myStruct{}

    for i := 0; i < 5; i++ {
        sl[i].h = uint64(i)
        sl[i].raw = make([]byte, 1024*1024)
    }
	
    var sum uint64 = 0
    for i := range sl {
        sum += sl[i].h
   }
}
```

2. Вариант 

```go
type myStruct struct {
    h     uint64
    cache [64]byte
    raw   []byte
}

func main() {
    sl := [5]myStruct{}

    for i := 0; i < 5; i++ {
        sl[i].h = uint64(i)
        sl[i].raw = make([]byte, 1024*1024)
    }
	
    var sum uint64 = 0
    for _, v := range &sl {
        sum += v.h
   }
}
```